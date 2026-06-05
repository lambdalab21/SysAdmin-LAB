A `403 Forbidden` response is a good troubleshooting exercise.

It already tells him something important:

> The request reached an HTTP server, and the server understood the request. This is probably not primarily an SSH, firewall, or basic connectivity problem.

Do not immediately give him the fix. Make him narrow the problem layer by layer.

A more natural wording: **“If he is getting a 403 error, give him troubleshooting lab exercises.”**

Red Hat’s Nginx documentation requires web content directories to have an appropriate SELinux context such as `httpd_sys_content_t`. Nginx also uses its `root` directive to construct the file path for a request and its `index` directive to choose files such as `index.html` when the client requests a directory. ([Red Hat Documentation](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/9/html-single/deploying_web_servers_and_reverse_proxies/index?utm_source=chatgpt.com "Deploying web servers and reverse proxies"))

# Troubleshooting Lab: Why Does Nginx Return `403 Forbidden`?

## Scenario

You installed Nginx on `app01`, created:

```text
/var/www/site1/public/
```

and copied `index.html` with:

```bash
scp ./public/index.html deploy@app01:/var/www/site1/public/
```

You then ran:

```bash
curl -v http://app01
```

but received:

```text
HTTP/1.1 403 Forbidden
```

Your goal is not merely to make the website work.

Your goal is to determine:

1. Which parts of the system are already working?
    
2. At which layer is the failure occurring?
    
3. Which command gives evidence for your conclusion?
    
4. What is the smallest correct fix?
    

Do not use:

```bash
chmod -R 777 ...
```

Do not disable SELinux.

Those actions may hide the cause rather than solve it correctly.

---

# Part 1: Interpret the Evidence

Run from the development machine:

```bash
curl -v http://app01
```

Record:

```text
HTTP status code:
Server header:
Response body:
```

## Questions

1. Did DNS or hostname resolution work?
    
2. Did the client reach a server?
    
3. Did a TCP connection to port 80 succeed?
    
4. Did Nginx return the response?
    
5. Is the firewall probably blocking port 80?
    

## Expected reasoning

If Nginx returns:

```text
403 Forbidden
```

then the request reached Nginx.

This means:

```text
client → network → firewall → port 80 → Nginx
```

is working well enough for Nginx to send an HTTP response.

The likely problem is now somewhere around:

```text
requested URL
→ selected Nginx server block
→ configured web root
→ index file
→ Unix permissions
→ SELinux policy
```

---

# Part 2: Check Whether the File Exists

Log in to `app01`:

```bash
ssh deploy@app01
```

Inspect the directory:

```bash
ls -la /var/www/site1/public/
```

Check the file directly:

```bash
ls -l /var/www/site1/public/index.html
```

Read it:

```bash
cat /var/www/site1/public/index.html
```

## Questions

1. Does `index.html` exist?
    
2. Is the filename spelled exactly correctly?
    
3. Is capitalization correct?
    
4. Is it in the expected directory?
    
5. Can the `deploy` user read it?
    

Linux filenames are case-sensitive:

```text
index.html
Index.html
INDEX.HTML
```

are different filenames.

---

# Part 3: Compare `/` with `/index.html`

From the development machine, run both:

```bash
curl -v http://app01/
```

```bash
curl -v http://app01/index.html
```

Record the result:

|URL|Status code|
|---|---|
|`/`||
|`/index.html`||

## Interpret the result

### Case A: `/` returns `403`, but `/index.html` returns `200`

Likely issue:

- Nginx is not finding the expected index file for a directory request.
    
- The wrong Nginx configuration may be active.
    
- The `index` directive may not be applied as expected.
    

Nginx’s default index filename is `index.html`. When a request ends in `/`, Nginx tries to locate an index file. If it cannot find one and directory listing is not enabled, the directory request can fail rather than displaying a file list. ([Nginx](https://nginx.org/en/docs/http/ngx_http_index_module.html?utm_source=chatgpt.com "Module ngx_http_index_module"))

### Case B: Both return `403`

Likely issue:

- Unix permissions
    
- SELinux context
    
- wrong server block or wrong `root`
    
- an explicit access restriction in Nginx configuration
    

### Case C: `/index.html` returns `404`

Likely issue:

- Nginx is looking in a different directory
    
- the wrong server block is handling the request
    
- `root` does not point to `/var/www/site1/public`
    

---

# Part 4: Inspect the Nginx Error Log

On `app01`:

```bash
sudo tail -n 50 /var/log/nginx/site1.error.log
```

Also inspect the general Nginx error log:

```bash
sudo tail -n 50 /var/log/nginx/error.log
```

Then watch the log while making another request:

```bash
sudo tail -f /var/log/nginx/site1.error.log
```

From another terminal:

```bash
curl -v http://app01/
```

If the site-specific log remains empty, watch:

```bash
sudo tail -f /var/log/nginx/error.log
```

## Look for messages such as

```text
directory index of ".../" is forbidden
```

```text
open() ".../index.html" failed (13: Permission denied)
```

```text
access forbidden by rule
```

## Questions

1. Which log file received the message?
    
2. What exact file path did Nginx try to access?
    
3. Did Nginx report a missing file, forbidden directory index, or permission denial?
    
4. Is Nginx using the server block you expected?
    

## Lesson

Do not guess before reading the logs.

The log often tells you whether the problem is:

```text
missing index file
```

or:

```text
permission denied
```

or:

```text
wrong configuration
```

---

# Part 5: Verify Which Nginx Configuration Is Active

Test the configuration:

```bash
sudo nginx -t
```

Display the effective configuration:

```bash
sudo nginx -T | less
```

Search inside `less`:

```text
/site1
```

Then inspect:

```bash
cat /etc/nginx/conf.d/site1.conf
```

Expected configuration:

```nginx
server {
    listen 80;
    server_name site1.local app01;

    root /var/www/site1/public;
    index index.html;

    access_log /var/log/nginx/site1.access.log;
    error_log  /var/log/nginx/site1.error.log;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

## Questions

1. Does `root` point to the correct directory?
    
2. Is `index index.html;` present?
    
3. Did you reload Nginx after creating or changing the config?
    
4. Is another `server` block receiving the request instead?
    
5. Is the request hostname listed under `server_name`?
    

After a valid configuration change:

```bash
sudo nginx -t
sudo systemctl reload nginx
```

---

# Part 6: Test the Virtual Host Explicitly

The hostname in the HTTP request affects which Nginx `server` block handles the request.

From the development machine:

```bash
curl -v http://app01/
```

Then test:

```bash
curl -v -H 'Host: site1.local' http://app01/
```

Also test:

```bash
curl -v -H 'Host: app01' http://app01/
```

## Questions

1. Do the responses differ?
    
2. Which hostname selects the `site1` configuration?
    
3. What does the HTTP `Host` header do?
    
4. Is another default Nginx site answering requests for `app01`?
    

If needed, add this to the client machine’s `/etc/hosts`:

```text
192.168.x.x site1.local
```

Then test:

```bash
curl -v http://site1.local/
```

## Lesson

One server can host multiple websites on the same IP address.

Nginx chooses a virtual host using the request hostname and the configured `server_name`.

---

# Part 7: Inspect Unix Permissions

Check every directory component in the path:

```bash
namei -l /var/www/site1/public/index.html
```

Also inspect them individually:

```bash
ls -ld /
ls -ld /var
ls -ld /var/www
ls -ld /var/www/site1
ls -ld /var/www/site1/public
ls -l /var/www/site1/public/index.html
```

Expected general pattern:

```text
drwxr-xr-x  /var
drwxr-xr-x  /var/www
drwxr-sr-x  /var/www/site1
drwxr-sr-x  /var/www/site1/public
-rw-r--r--  /var/www/site1/public/index.html
```

## Questions

1. Can Nginx traverse every directory in the path?
    
2. Does `index.html` have read permission?
    
3. What does `x` mean on a directory?
    
4. Why does a readable file still fail if a parent directory lacks `x` permission?
    
5. Does Nginx need write access?
    

## Important distinction

For a directory:

```text
r = list filenames
w = create, delete, or rename entries
x = enter or traverse the directory
```

Nginx needs to traverse the parent directories and read the file.

It normally does not need to modify static website files.

---

# Part 8: Test Unix Permissions as the Nginx User

Check which user Nginx runs as:

```bash
grep '^user' /etc/nginx/nginx.conf
```

Usually on AlmaLinux:

```text
user nginx;
```

Now test access as that user:

```bash
sudo -u nginx test -r /var/www/site1/public/index.html \
  && echo "nginx can read index.html" \
  || echo "nginx cannot read index.html"
```

Test directory traversal:

```bash
sudo -u nginx test -x /var/www/site1/public \
  && echo "nginx can traverse public/" \
  || echo "nginx cannot traverse public/"
```

Try to read the file:

```bash
sudo -u nginx cat /var/www/site1/public/index.html
```

## Questions

1. Can the Nginx user read the file?
    
2. Can the Nginx user traverse the directory?
    
3. If these tests succeed but HTTP still returns `403`, what security layer has not yet been checked?
    

## Lesson

Traditional Unix permissions are only one security layer.

On AlmaLinux, SELinux may also control access.

---

# Part 9: Inspect SELinux

Check whether SELinux is enforcing policy:

```bash
getenforce
```

Possible output:

```text
Enforcing
```

Inspect SELinux labels:

```bash
ls -ldZ /var/www/site1
ls -ldZ /var/www/site1/public
ls -lZ /var/www/site1/public/index.html
```

Look for a type such as:

```text
httpd_sys_content_t
```

Red Hat documents `httpd_sys_content_t` as the SELinux type for read-only web content. It allows the web server domain to read files but not modify them. ([Red Hat Documentation](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/9/html-single/deploying_web_servers_and_reverse_proxies/index?utm_source=chatgpt.com "Deploying web servers and reverse proxies"))

## Questions

1. Is SELinux enabled?
    
2. Is it enforcing policy?
    
3. What SELinux type does `index.html` have?
    
4. Is the type `httpd_sys_content_t`?
    
5. How is an SELinux label different from Unix owner/group permissions?
    

---

# Part 10: Restore the Expected SELinux Labels

First try the least invasive correction:

```bash
sudo restorecon -Rv /var/www/site1
```

Inspect labels again:

```bash
ls -lZR /var/www/site1
```

Test:

```bash
curl -v http://app01/
```

## Questions

1. Did `restorecon` change any labels?
    
2. Did the HTTP status change?
    
3. Why is `restorecon` better than disabling SELinux?
    

Red Hat documents `restorecon` as the tool used to apply expected SELinux file contexts. ([Red Hat Documentation](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/9/html/using_selinux/configuring-selinux-for-applications-and-services-with-non-standard-configurations_using-selinux?utm_source=chatgpt.com "Chapter 4. Configuring SELinux for applications and ..."))

---

# Part 11: Add a Persistent SELinux Rule If Needed

If the correct label is still not applied, install the package that provides `semanage` if necessary:

```bash
sudo dnf install policycoreutils-python-utils
```

Add a persistent rule:

```bash
sudo semanage fcontext -a \
  -t httpd_sys_content_t \
  '/var/www/site1(/.*)?'
```

Apply it:

```bash
sudo restorecon -Rv /var/www/site1
```

Verify:

```bash
ls -lZR /var/www/site1
```

Test:

```bash
curl -v http://app01/
```

Red Hat’s Nginx documentation uses `semanage fcontext` followed by `restorecon` to label custom web-content directories persistently. ([Red Hat Documentation](https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/10/html-single/deploying_web_servers_and_reverse_proxies/index?utm_source=chatgpt.com "Deploying web servers and reverse proxies | Red Hat Enterprise Linux"))

## Questions

1. What does `semanage fcontext -a` add?
    
2. What does `-t httpd_sys_content_t` specify?
    
3. What does this expression match?
    

```text
/var/www/site1(/.*)?
```

4. Why run `restorecon` after `semanage fcontext`?
    
5. Why is this preferable to manually running `chcon`?
    

---

# Part 12: Check for SELinux Denials

If the site still fails, inspect recent SELinux denials:

```bash
sudo ausearch -m AVC -ts recent
```

If available, also try:

```bash
sudo journalctl --since "10 minutes ago" | grep -i avc
```

## Questions

1. Is SELinux denying Nginx access?
    
2. Which file path appears in the denial?
    
3. Which source process appears?
    
4. Which target type appears?
    
5. Does the denial suggest a labeling mistake?
    

Do not blindly generate and install a custom SELinux policy.

First determine whether the file simply has the wrong label.

---

# Part 13: Use a Controlled Comparison

Check whether the default Nginx page works:

```bash
curl -v http://app01/
```

If your custom site is selected by hostname, explicitly test the default site and the custom site:

```bash
curl -v -H 'Host: unknown.local' http://app01/
```

```bash
curl -v -H 'Host: site1.local' http://app01/
```

Inspect the default content directory:

```bash
ls -lZ /usr/share/nginx/html/
```

Compare it with:

```bash
ls -lZ /var/www/site1/public/
```

## Questions

1. Does the default Nginx site work?
    
2. Does the custom site fail?
    
3. What differs between the working and failing directories?
    
4. Are the Unix permissions different?
    
5. Are the SELinux labels different?
    
6. Is the selected server block different?
    

## Lesson

A working control case helps isolate the variable.

---

# Part 14: Final Diagnosis Report

Write a short report:

```text
Symptom:
curl -v http://app01 returned:

Evidence that the network path worked:

Evidence from the Nginx error log:

Requested URL:

Nginx root directory:

Expected file path:

Did index.html exist?

Could the nginx user read the file?

Could the nginx user traverse all parent directories?

SELinux mode:

SELinux type on index.html:

Root cause:

Smallest correct fix:

How I verified the fix:
```

---

# Troubleshooting Order to Remember

Use this order:

```text
1. Read the HTTP response
2. Test / and /index.html separately
3. Read Nginx error logs
4. Inspect effective Nginx config
5. Verify Host header / server_name
6. Confirm file existence
7. Check Unix permissions with namei
8. Test access as the nginx user
9. Inspect SELinux labels
10. Restore or persist the correct SELinux context
11. Verify with curl and logs
```

---

# Completion Checkpoint

Explain:

1. Why does `403` differ from connection timeout?
    
2. Why does `403` differ from `404`?
    
3. What is the difference between `/` and `/index.html`?
    
4. What does the Nginx `root` directive do?
    
5. What does the Nginx `index` directive do?
    
6. What does `server_name` do?
    
7. What does the HTTP `Host` header do?
    
8. Why must directories have execute permission?
    
9. Why is checking only `index.html` permissions insufficient?
    
10. What does `namei -l` reveal?
    
11. Why test file access as the `nginx` user?
    
12. What is SELinux?
    
13. How do SELinux labels differ from Unix permissions?
    
14. What does `httpd_sys_content_t` mean?
    
15. Why use `restorecon` rather than disabling SELinux?
    
16. Why is `chmod 777` not an acceptable fix?
    

## Likely causes in this specific lab

Given this setup, investigate these first:

1. `index.html` was copied into the wrong directory.
    
2. The active Nginx `root` points somewhere other than `/var/www/site1/public`.
    
3. `curl http://app01` selects a default server block instead of `site1`.
    
4. One of the parent directories lacks execute permission.
    
5. SELinux labels are incorrect after creating or copying the custom web directory.
    

On AlmaLinux, do not treat SELinux as an annoying obstacle to switch off. It is part of the lesson. A junior infrastructure professional must learn that traditional permissions and SELinux policy are separate security layers.