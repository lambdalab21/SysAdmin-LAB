# Shotts Chapter 24: Writing Your First Script

Use this guide with William Shotts, *The Linux Command Line*, Chapter 24, "Writing Your First Script."

One-time lab setup:

```bash
mkdir -p ~/tlcl-ch24-lab/{bin,scripts,tmp,output}
cd ~/tlcl-ch24-lab
```

---
# Session 3: Executable Permissions and Running Scripts

## Read before exercise

Read this Chapter 24 section:

```text
Executable Permissions
```

He should gain this idea:

```text
A script file is not automatically a runnable program.
It needs executable permission if you want to run it directly.
```

---

# After-reading questions

Answer before typing:

1. What does executable permission allow? It allows a file to be run as a program. 
2. Which command changes file permissions? Chmod. 
3. What does `chmod +x scriptname` mean? It adds the executable permission to the script. 
4. How can `ls -l` show whether a file is executable? It displays x in the permission bits. 
5. Why is `./system-info` different from `system-info`? ./ tells the shell to run the file from the current directory. 

---

# Exercise 1: Inspect permissions

Run:

```bash
cd ~/tlcl-ch24-lab/scripts
ls -l system-info
```

Answer:

```text
Owner permissions: rwx. 
Group permissions: r-x or r--
Other permissions: r-x or r--
Is x present? Yes. 
```

---

# Exercise 2: Try direct execution before chmod

Predict first:

```text
What do I expect ./system-info to do before chmod +x? Show a 'permission denied' error. 
```

Run:

```bash
./system-info
```

If permission is denied, that is expected.

Now answer:

```text
Did the error come from Bash syntax or file permission? File permission. 
How do I know? The error says permission is denied, not a syntax error. 
```

---

# Exercise 3: Add executable permission

Run:

```bash
chmod +x system-info
ls -l system-info
```

Now predict:

```text
What changed in ls -l? An x was added to the file's permissions. 
Will ./system-info run now? Yes. 
```

Run:

```bash
./system-info
```

---

# Exercise 4: Compare three ways to run

Run:

```bash
bash system-info
./system-info
system-info
```

The last one will probably fail unless the script directory is in `PATH`.

Fill in:

```text
bash system-info worked because bash reads and executes the script. 
./system-info worked because The script has executable permissions. 
system-info failed or worked because It depends on whether or not the current directory in the path. 
```

---

# Documentation habit

Use:

```bash
man chmod
chmod --help | head -n 20
```

Answer:

1. What does `chmod` change? File permissions. 
2. What does `+x` add? Execute Permissions. 
3. What section of `man chmod` was most useful? The description or examples section
4. Was `--help` faster for quick syntax? Yes. 

---

# Explain-back

Explain:

```bash
chmod +x system-info
./system-info
```

Use:

```text
chmod +x means adding executable permission to the file. 
./ runs the program from the current directory. 
The system reads the shebang and uses the specified interpreter to run the script. 
```

---

# Read after exercise

Reread “Executable Permissions.”

This time, read with this question:

```text
Why is executable permission a safety feature? It prevents files from being run. 
```

---

# Session 3 checkpoint

He is ready for Session 4 only if he can answer:

1. Why did `bash system-info` work without executable permission? Because bash interpreted the script directly. 
2. Why did `./system-info` need executable permission? Because it was run as an executable program. 
3. What does `chmod +x` do? Adds execute permission. 
4. Why does `system-info` not automatically work from the current directory? Because the current directory is usually not included in the path environment variable. 
