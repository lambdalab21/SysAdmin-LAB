# Reflection Handout: How to Improve Future Lab Work

## Purpose

You completed the `site1` update deployment lab. The important question is not only:

```text
Did the website update?
```

The more important question is:

```text
Did I practice the habits of a careful troubleshooter and operator?
```

This handout is for reflection after this lab and before future labs.

---

# 1. What You Did Well

## You understood the source of truth

You correctly identified that the local project directory is the source of truth:

```text
local project directory = source of truth
server directory = deployed copy
```

That is an important deployment idea.

A weak habit is:

```text
edit random files wherever they happen to be
```

A stronger habit is:

```text
edit locally
deploy intentionally
verify remotely
```

You are moving toward the stronger habit.

---

## You verified with more than one kind of evidence

You used several useful forms of evidence:

```text
filesystem check
server grep
curl
browser
Nginx access log
Nginx error log
```

That is good.

A deployment is not proven only by:

```text
scp finished
```

A deployment is better proven by:

```text
the server file changed
the HTTP response shows the new content
the access log shows the request
the error log does not show a new problem
```

This is the right direction.

---

## You learned the weakness of `scp`

You correctly observed that deleting `practice.html` locally did not remove it from the server.

That proves:

```text
scp copies files
scp does not synchronize directory state
scp does not remove stale server files
```

That is exactly why `rsync --delete` is the next tool.

---

# 2. Main Weaknesses to Fix

## Weakness 1: Path discipline is still weak

You wrote:

```text
My local project directory: ~/code/projects/site1/public
```

But the lab expected the project directory itself, not only the public directory.

Better:

```text
My local project directory: ~/code/projects/site1
My local public directory: ~/code/projects/site1/public
```

Why this matters:

```text
project directory = whole working area
public directory = files safe to deploy
```

Do not blur these two.

## Stop and think

Before deploying, ask:

```text
Am I in the project root?
Am I deploying only public files?
Am I accidentally deploying notes, scripts, .git, .env, or private files?
```

Command:

```bash
pwd
find ./public -maxdepth 2 -type f -print
```

Do not deploy until the output makes sense.

---

## Weakness 2: Source and destination confusion

You wrote:

```text
If this succeeds, the changed file should appear at:
/code/projects/site1/public/
```

That is the local path, not the remote deployment path.

The correct answer should be:

```text
If this succeeds, the changed file should appear at:
/var/www/site1/public/index.html
```

This matters because deployment is about moving from:

```text
local source → remote destination
```

If you confuse those, you may copy the wrong direction someday.

## Feynman explanation

Explain `scp` as if teaching a younger student:

```text
scp takes a file from my development machine and copies it through SSH
to a path on the server. The colon separates the remote host from the
remote path.
```

If you cannot say which side is local and which side is remote, do not run the command yet.

---

## Weakness 3: Typos in commands and paths need more attention

You wrote:

```text
./public/idnex.html
```

instead of:

```text
./public/index.html
```

A typo like this can waste a lot of time.

Do not treat spelling as a small issue in Linux. Linux usually does exactly what you typed, not what you meant.

## Slow-down habit

Before pressing Enter on a command that copies, deletes, changes permissions, or edits config, pause and point to each part:

```text
scp
./public/index.html
deploy@app01
/var/www/site1/public/
```

Say out loud:

```text
This copies index.html from my local public directory
to the remote Nginx public directory on app01.
```

Then run it.

---

## Weakness 4: Some answers are correct but too vague

Example:

```text
Which files exist on the server? Mirror of deployed public contents.
```

This is conceptually okay, but it is not evidence.

Better:

```text
Server files:
- /var/www/site1/public/index.html
- /var/www/site1/public/style.css
- /var/www/site1/public/practice.html
```

A good lab answer should often include exact output, not just a summary.

## Rule

When the question asks what exists, include command output.

Bad:

```text
It has the deployed contents.
```

Better:

```text
Command:
ssh deploy@app01 'find /var/www/site1/public -type f -print'

Output:
/var/www/site1/public/index.html
/var/www/site1/public/style.css
/var/www/site1/public/practice.html
```

---

## Weakness 5: Verification should be more precise

You wrote:

```text
What exact output proves it? grep on the server showed the new text.
```

Good idea, but better would be the exact line:

```text
Output:
12:<p>Deployment Practice Version 2</p>
```

The phrase “grep showed the new text” is a conclusion. The exact line is evidence.

## Future rule

For every important verification, write:

```text
Command:
Exact output:
What this proves:
What this does not prove:
```

---

## Weakness 6: Be careful with HTTP vs HTTPS

You wrote:

```text
curl -s https://app01 | grep 'Deployment practice'
```

But this lab was using:

```text
http://app01/
```

Nginx was serving plain HTTP on port 80, not HTTPS on port 443.

This matters.

```text
http  = port 80, no TLS
https = port 443, TLS
```

Do not switch protocols casually. It changes the test.

## Stop point

Before using `curl`, ask:

```text
Which protocol am I testing?
Which hostname or IP am I testing?
Which Nginx server block should answer?
Which path am I requesting?
```

Example:

```bash
curl -v http://app01/
```

---

## Weakness 7: Fix spelling and terminology

You wrote:

```text
synchrnoizeation
```

Better:

```text
synchronization
```

This is not about English perfection. It is about precision.

Technical work depends on precision:

```text
index.html is not idnex.html
http is not https
source is not destination
project directory is not public directory
copy is not synchronize
```

Precision in language supports precision in commands.

---

# 3. The Habit Loop for Future Labs

Use this short loop for every important command.

## Before the command

Write:

```text
Question:
Command:
Expected result:
Risk if wrong:
```

Example:

```text
Question:
Did index.html deploy to the server?

Command:
ssh deploy@app01 'grep -n "Deployment Practice" /var/www/site1/public/index.html'

Expected result:
A line containing Deployment Practice Version 2.

Risk if wrong:
I may be testing the wrong file or wrong server.
```

## After the command

Write:

```text
Exact output:
What this proves:
What this does not prove:
Next check:
```

Example:

```text
Exact output:
12:<p>Deployment Practice Version 2</p>

What this proves:
The server copy of index.html contains the new text.

What this does not prove:
It does not prove Nginx served it over HTTP.

Next check:
curl http://app01/ | grep "Deployment Practice"
```

This prevents false confidence.

---

# 4. Green Checks

Use checkboxes, but do not treat them as busywork.

A checkbox is useful only if it represents evidence.

## Deployment checklist

```text
[ ] I am in the project root.
[ ] I listed local public files.
[ ] I know the remote target path.
[ ] I changed exactly one thing first.
[ ] I verified the local change before deployment.
[ ] I copied the intended file.
[ ] I verified the server file directly.
[ ] I verified through HTTP with curl.
[ ] I checked the Nginx access log.
[ ] I checked the Nginx error log.
[ ] I wrote what the deployment did not prove.
[ ] I recorded one lesson for next time.
```

Do not check a box unless you can point to the evidence.

---

# 5. Better Final Reflection Template

Use this after each lab.

```text
Task:

Source of truth:

Command that changed the system:

Strongest evidence that it worked:

Evidence from filesystem:

Evidence from HTTP/browser:

Evidence from logs:

One thing the successful command did NOT prove:

One mistake or near-mistake:

One habit I practiced:

One habit to improve next time:
```

## Example from this lab

```text
Task:
Update site1 and deploy with scp.

Source of truth:
~/code/projects/site1/public/

Command that changed the system:
scp ./public/index.html deploy@app01:/var/www/site1/public/

Strongest evidence that it worked:
curl http://app01/ showed the new text, and the access log showed 200.

Evidence from filesystem:
Server grep found the new text in /var/www/site1/public/index.html.

Evidence from HTTP/browser:
curl and browser showed the updated page.

Evidence from logs:
Nginx access log showed a 200 request for / or /index.html.
Nginx error log showed no new errors.

One thing the successful command did NOT prove:
It did not prove that deleted local files were removed from the server.

One mistake or near-mistake:
I almost treated scp success as full deployment verification.

One habit I practiced:
I verified with filesystem, HTTP, and logs.

One habit to improve next time:
Be more precise with paths and quote exact output.
```

---

# 6. What to Change Next Time

For the next deployment lab, improve these three things.

## Change 1: Always write exact paths

Do not write:

```text
the server
the file
the local folder
```

Write:

```text
~/code/projects/site1/public/index.html
/var/www/site1/public/index.html
http://app01/
```

## Change 2: Always quote one exact output line

Do not write:

```text
curl worked
grep showed it
no errors
```

Write:

```text
curl output contained:
<p>Deployment Practice Version 2</p>

access log contained:
"GET / HTTP/1.1" 200
```

## Change 3: Separate evidence from conclusion

Evidence:

```text
curl returned HTTP/1.1 200 OK
```

Conclusion:

```text
Nginx served the page successfully
```

Evidence first. Conclusion second.

---

# 7. Short Feynman Check

Explain this lab to a beginner in five sentences.

Fill in:

```text
I edited the website on the development machine because:

I copied the file with scp because:

I checked the server file because:

I checked curl because:

I checked logs because:
```

Good version:

```text
I edited the website on the development machine because that is the source of truth.
I copied the file with scp because scp securely transfers files over SSH.
I checked the server file because scp success alone does not prove the correct file is present.
I checked curl because Nginx must serve the changed file over HTTP.
I checked logs because logs show whether the request reached Nginx and whether Nginx reported errors.
```

If you can explain it simply, you probably understand it.

If you cannot explain it simply, slow down and inspect the evidence again.
