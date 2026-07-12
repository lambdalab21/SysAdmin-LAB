#Book/The-Linux-Command-Line  #Author/Shotts 
#process

Chapter 10 of _The Linux Command Line_, 3rd ed. is **“Processes.”** It covers how processes work, viewing processes, `top`, foreground and background jobs, stopping and resuming jobs, process priority, signals, `kill`, `killall`, hang-up-proof processes, shutdown, and related commands. ([オライリー](https://www.oreilly.com/library/view/the-linux-command/0642572230227/ "The Linux Command Line, 3rd Edition [Book]"))

Use one set at a time. Do not show the answer key until he has answered.

# Chapter 10 Quiz Sets: Processes

## Set 1: What is a process?

1. What is a process?
    
2. What is the difference between a program and a process?
    
3. What does PID stand for?
    
4. Can two running instances of the same program have different PIDs?
    
5. Why does the operating system assign a PID to each process?
    

---

## Set 2: Parent and child processes

1. What is a parent process?
    
2. What is a child process?
    
3. When a shell starts a command such as:
    
    ```bash
    sleep 60
    ```
    
    which process is usually the parent?
    
4. What does PPID stand for?
    
5. Why is the parent-child relationship useful when troubleshooting processes?
    

---

## Set 3: Inspecting processes with `ps`

1. What command displays a snapshot of running processes?
    
2. What does this command usually show?
    
    ```bash
    ps
    ```
    
3. What additional information does this command show?
    
    ```bash
    ps x
    ```
    
4. What does this command show?
    
    ```bash
    ps aux
    ```
    
5. Is `ps` a continuously updating display or a snapshot?
    

---

## Set 4: Reading `ps` output

Examine this simplified output:

```text
USER       PID  %CPU %MEM  COMMAND
alice     2401   0.0  0.1  bash
alice     2522   0.0  0.0  sleep 300
root       811   0.1  1.2  nginx
```

1. Which process is owned by `root`?
    
2. What is the PID of `sleep 300`?
    
3. Which process is consuming the most memory?
    
4. Could Alice normally send signals to the Nginx process without elevated privileges?
    
5. Why is the owner column important?
    

---

## Set 5: Dynamic monitoring with `top`

1. What is the main difference between `ps` and `top`?
    
2. What command starts an interactive process monitor?
    
3. Why is `top` useful when a system feels slow?
    
4. What does pressing `q` inside `top` usually do?
    
5. If one process consistently consumes most of the CPU, what should the student do before killing it?
    

---

## Set 6: Foreground processes

1. What is a foreground process?
    
2. When you run:
    
    ```bash
    sleep 300
    ```
    
    does the shell immediately give you a new prompt?
    
3. Which keyboard shortcut usually interrupts a foreground process?
    
4. What signal is usually sent when pressing:
    
    ```text
    Ctrl-c
    ```
    
5. Why is `Ctrl-c` preferable to closing the terminal window?
    

---

## Set 7: Background processes

1. What does the trailing `&` do here?
    
    ```bash
    sleep 300 &
    ```
    
2. What command shows jobs started from the current shell?
    
3. What is the difference between a job number and a PID?
    
4. How do you refer to job number 1 in a shell job-control command?
    
5. Why might a sysadmin run a long command in the background?
    

---

## Set 8: Bringing jobs to the foreground

Assume this command was run:

```bash
sleep 300 &
```

1. What command lists the current shell’s jobs?
    
2. What command brings job number 1 into the foreground?
    
3. After bringing a process to the foreground, which shortcut can interrupt it?
    
4. Does `fg` operate on a job number or require a PID?
    
5. Why is foregrounding a job useful?
    

---

## Set 9: Stopping and resuming a job

1. What keyboard shortcut pauses a foreground process?
    
2. What signal is usually associated with pausing a process from the keyboard?
    
3. After pausing a process, what command resumes it in the background?
    
4. After pausing a process, what command resumes it in the foreground?
    
5. What is the difference between stopping a process and terminating it?
    

---

## Set 10: Job control sequence

A student runs:

```bash
sleep 600
```

Then presses:

```text
Ctrl-z
```

Answer the following:

1. Is the process terminated?
    
2. What command should show the paused job?
    
3. What command resumes it in the background?
    
4. What command brings it back to the foreground?
    
5. What command could terminate it after retrieving its PID?
    

---

## Set 11: Signals

1. What is a signal?
    
2. Why are signals useful for process management?
    
3. What is the difference between asking a process to terminate and forcibly killing it?
    
4. Which signal should usually be tried before `SIGKILL`?
    
5. Why is `SIGKILL` not the best first choice?
    

---

## Set 12: Common signal numbers

Match each signal number to its common meaning:

```text
1
2
9
15
18
19
```

1. Which number is `SIGHUP`?
    
2. Which number is `SIGINT`?
    
3. Which number is `SIGKILL`?
    
4. Which number is `SIGTERM`?
    
5. Which numbers are commonly associated with continuing and stopping a process?
    

---

## Set 13: Using `kill`

1. What does this command do?
    
    ```bash
    kill 2522
    ```
    
2. Which signal does `kill` send by default?
    
3. What does this command do?
    
    ```bash
    kill -9 2522
    ```
    
4. Which version is normally better to try first?
    
5. Why should you verify a PID before sending a signal?
    

---

## Set 14: Using `killall`

1. What is the basic purpose of `killall`?
    
2. What does this command attempt to do?
    
    ```bash
    killall sleep
    ```
    
3. Why is `killall` potentially more dangerous than `kill PID`?
    
4. Should you use `killall` casually on a multi-user production server?
    
5. What should you inspect before running `killall`?
    

---

## Set 15: Process ownership

1. Can a regular user normally kill another user’s processes?
    
2. Can a regular user normally kill their own processes?
    
3. Why might this fail?
    
    ```bash
    kill 811
    ```
    
4. How could an authorized administrator send a signal to a root-owned process?
    
5. Why is process ownership a security feature?
    

---

## Set 16: Process priority

1. What does process priority control?
    
2. What is the purpose of `nice`?
    
3. What is the purpose of `renice`?
    
4. Does a “nicer” process demand more CPU time or yield more readily to other processes?
    
5. Why might a sysadmin lower the priority of a CPU-intensive background job?
    

---

## Set 17: `nice` and `renice`

1. What does this command do conceptually?
    
    ```bash
    nice long-running-command
    ```
    
2. What does this command attempt to modify?
    
    ```bash
    renice 10 -p 2522
    ```
    
3. What does `-p 2522` identify?
    
4. Is priority adjustment the same as stopping a process?
    
5. Give one situation in which `nice` would be appropriate.
    

---

## Set 18: Hang-up-proof processes

1. What may happen to a process when its terminal session closes?
    
2. What is the basic purpose of `nohup`?
    
3. What does this command attempt to achieve?
    
    ```bash
    nohup long-command &
    ```
    
4. Why is the trailing `&` still useful with `nohup`?
    
5. Why might `tmux` be preferable to `nohup` for an interactive remote task?
    

---

## Set 19: Shell jobs versus system processes

1. Does this command show every process on the machine?
    
    ```bash
    jobs
    ```
    
2. What does `jobs` show?
    
3. Does this command show only jobs started from the current shell?
    
    ```bash
    ps aux
    ```
    
4. Why might a process appear in `ps` but not in `jobs`?
    
5. Which tool should you use when investigating a process started by a service manager?
    

---

## Set 20: Shutdown commands

1. Why should a Linux system normally be shut down with a shutdown command rather than by abruptly cutting power?
    
2. What kind of work may need to finish before shutdown?
    
3. Which users are normally allowed to shut down the system?
    
4. Why is rebooting not the first troubleshooting method a Linux administrator should use?
    
5. What should the student inspect before rebooting a misbehaving server?
    

---

# Applied quiz sets

## Set 21: A runaway command

A student accidentally runs:

```bash
yes
```

The terminal rapidly prints lines of output.

1. Is `yes` running in the foreground or background?
    
2. What shortcut should stop it?
    
3. Why is opening another terminal and using `kill -9` unnecessary as a first response?
    
4. What process-management concept does this demonstrate?
    
5. What should the student do if `Ctrl-c` does not work?
    

---

## Set 22: A paused process

A student runs:

```bash
sleep 900
```

Then presses `Ctrl-z`.

The shell prints something similar to:

```text
[1]+  Stopped    sleep 900
```

1. Is `sleep` still present as a process?
    
2. What does `[1]` represent?
    
3. What command resumes it in the background?
    
4. What command brings it back to the foreground?
    
5. What command displays the current shell’s jobs?
    

---

## Set 23: Choosing the right signal

A student wants to stop a process with PID `4102`.

1. What command should usually be tried first?
    
2. What signal does that command send by default?
    
3. What stronger command could be used if the process refuses to terminate?
    
4. Why should the stronger command not be the first attempt?
    
5. What should be checked before sending either command?
    

---

## Set 24: Process inspection

A server feels slow. A student runs:

```bash
top
```

and sees that one process consistently uses nearly all available CPU.

1. Is high CPU usage automatically proof that the process is broken?
    
2. What information should the student record before terminating it?
    
3. Why should the process owner matter?
    
4. Why should the student check logs or service status?
    
5. What is the danger of killing a process without understanding its role?
    

---

## Set 25: Remote session interrupted

A student starts a long-running command over SSH and then closes the laptop.

1. What may happen to the remote process?
    
2. What tool from Chapter 10 can help a non-interactive command survive logout?
    
3. Write a command pattern that uses that tool and places the process in the background.
    
4. Why might `tmux` be better if the student wants to reconnect and inspect the live terminal session?
    
5. What lesson should the student learn before starting a long remote task?
    

---

## Set 26: Service troubleshooting

Nginx is running on `app01`, but a student wants to know which process owns the service.

1. Which command can display running processes?
    
2. Which command can provide a continuously updating view?
    
3. Why is this safer than immediately killing all Nginx processes?
    
4. Why may restarting a service through `systemctl` be preferable to manually killing processes?
    
5. What should be checked after a restart?
    

---

# Answer key

## Set 1

1. A running instance of a program.
    
2. A program is executable code stored on disk; a process is a running instance.
    
3. Process ID.
    
4. Yes.
    
5. So the operating system and users can identify and manage individual processes.
    

## Set 2

1. A process that starts another process.
    
2. A process started by another process.
    
3. The shell.
    
4. Parent Process ID.
    
5. It helps identify where a process came from and how related processes are organized.
    

## Set 3

1. `ps`
    
2. Processes associated with the current terminal session.
    
3. Processes owned by the user, including those without a controlling terminal.
    
4. A broad list of running processes with detailed information.
    
5. A snapshot.
    

## Set 4

1. `nginx`
    
2. `2522`
    
3. `nginx`
    
4. Usually not.
    
5. A user’s ability to manage a process depends partly on ownership.
    

## Set 5

1. `ps` is a snapshot; `top` updates repeatedly.
    
2. `top`
    
3. It helps identify processes consuming CPU or memory.
    
4. Quit.
    
5. Identify the process, owner, purpose, and related logs first.
    

## Set 6

1. A process attached to the terminal and currently controlling the shell prompt.
    
2. No.
    
3. `Ctrl-c`
    
4. `SIGINT`
    
5. It interrupts the intended process while preserving the shell session.
    

## Set 7

1. It starts the command in the background.
    
2. `jobs`
    
3. A job number is shell-specific; a PID is assigned by the operating system.
    
4. `%1`
    
5. To continue using the shell while the command runs.
    

## Set 8

1. `jobs`
    
2. `fg %1`
    
3. `Ctrl-c`
    
4. A shell job number.
    
5. It returns terminal control to that process for interaction or interruption.
    

## Set 9

1. `Ctrl-z`
    
2. `SIGTSTP`
    
3. `bg`
    
4. `fg`
    
5. A stopped process is paused but still exists. A terminated process exits.
    

## Set 10

1. No.
    
2. `jobs`
    
3. `bg %1`
    
4. `fg %1`
    
5. For example:
    
    ```bash
    kill PID
    ```
    

## Set 11

1. A notification sent to a process.
    
2. Signals allow processes to be interrupted, stopped, resumed, reloaded, or terminated.
    
3. Graceful termination gives a process a chance to clean up; forced termination does not.
    
4. `SIGTERM`
    
5. It prevents orderly cleanup and may leave incomplete work or inconsistent state.
    

## Set 12

1. `1`
    
2. `2`
    
3. `9`
    
4. `15`
    
5. Continue: `18`; stop: `19`
    

## Set 13

1. Sends the default termination signal to PID `2522`.
    
2. `SIGTERM`
    
3. Sends `SIGKILL` to PID `2522`.
    
4. Plain `kill 2522`
    
5. PIDs can change, and killing the wrong process can disrupt the system.
    

## Set 14

1. Send a signal to processes by name.
    
2. Terminate processes named `sleep`.
    
3. It may affect multiple processes.
    
4. No.
    
5. Inspect matching processes and their owners.
    

## Set 15

1. Usually not.
    
2. Usually yes.
    
3. The process may belong to another user or to `root`.
    
4. For example:
    
    ```bash
    sudo kill PID
    ```
    
5. It prevents users from disrupting unrelated processes.
    

## Set 16

1. How CPU scheduling favors or deprioritizes a process.
    
2. Start a command with an adjusted scheduling priority.
    
3. Change the priority of an existing process.
    
4. Yield more readily.
    
5. To reduce interference with interactive or critical work.
    

## Set 17

1. Starts the command with a lower scheduling priority than normal.
    
2. The scheduling priority of PID `2522`.
    
3. A process ID.
    
4. No.
    
5. Running a compression, indexing, or batch-processing job without disrupting interactive use.
    

## Set 18

1. It may receive a hang-up signal and terminate.
    
2. Make a command more resistant to terminal hangups.
    
3. Run a long command so it can continue after logout while returning shell control.
    
4. Without `&`, the shell still waits for the command.
    
5. `tmux` preserves an interactive session that can be reattached later.
    

## Set 19

1. No.
    
2. Jobs managed by the current shell.
    
3. No. It shows a much broader process list.
    
4. It may have been started by another shell, another user, a service manager, or the system.
    
5. Use tools such as `ps`, `top`, and service-management commands such as `systemctl status`.
    

## Set 20

1. Filesystems and processes should be shut down cleanly.
    
2. Buffered writes, process cleanup, and filesystem synchronization.
    
3. Authorized users, often through `sudo`.
    
4. Rebooting destroys evidence and teaches nothing about the cause.
    
5. Processes, logs, service status, resource usage, and network state.
    

## Set 21

1. Foreground.
    
2. `Ctrl-c`
    
3. `SIGINT` is sufficient in the normal case.
    
4. Interrupting a foreground process.
    
5. Open another terminal, identify the PID, and send an appropriate signal.
    

## Set 22

1. Yes.
    
2. Shell job number 1.
    
3. `bg %1`
    
4. `fg %1`
    
5. `jobs`
    

## Set 23

```bash
kill 4102
```

2. `SIGTERM`
    

```bash
kill -9 4102
```

4. It does not allow graceful cleanup.
    
5. Confirm the PID, owner, and purpose of the process.
    

## Set 24

1. No. The process may be performing expected work.
    
2. PID, owner, command, resource usage, and timing.
    
3. It helps identify who started the process and whether the student has authority to manage it.
    
4. The process may be a managed service, and logs may explain the activity.
    
5. It may interrupt legitimate work or damage service availability.
    

## Set 25

1. It may terminate when the SSH session ends.
    
2. `nohup`
    

```bash
nohup long-command &
```

4. `tmux` allows reattachment to the running terminal session.
    
5. Decide whether the task should use `nohup`, `tmux`, a service manager, or another supervised mechanism before starting it.
    

## Set 26

1. `ps`
    
2. `top`
    
3. Inspection identifies what is actually running and who owns it.
    
4. The service manager handles service lifecycle more predictably.
    
5. Service status, logs, listening ports, and an HTTP request such as:
    
    ```bash
    curl -v http://app01
    ```
    

# Recommended sequence

|Stage|Quiz sets|
|---|---|
|Initial reading|1–6|
|After trying basic commands|7–10|
|After reading about signals|11–18|
|Review|19–20|
|Applied understanding|21–26|

The important dividing line is this:

```text
weak understanding:
memorize ps, top, kill, jobs

strong understanding:
identify the process
→ understand who owns it
→ decide whether to stop, resume, reprioritize, or terminate it
→ choose the least destructive action
→ verify the result
```

A student who reaches immediately for `kill -9` has not understood Chapter 10.