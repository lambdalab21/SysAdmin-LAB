#LAB #deployment
#ssh
You fixed the SSH issue, but the bigger win is not that SSH works now. The bigger win is that you learned how to follow evidence.

Here is the blunt version: your first attempt had good data, but weak interpretation. The logs were telling you the answer, but you rushed past them.

When you saw:

```text
Accepted password for deploy from 192.168.0.107 port 38652 ssh2
```

you should have stopped. That one line proves several things:

```text
the server was reachable
sshd was running
port 22 was reachable
the username was deploy
the client IP was 192.168.0.107
password authentication succeeded
```

So the problem was not “SSH is broken” and not “password failed.” The problem came later.

Then this line narrowed it further:

```text
error: PAM: pam_open_session(): Permission denied
```

That means the failure was during PAM session setup.

Then this line pointed to the exact kind of mistake:

```text
PAM pam_parse: expecting return value; [...optimal]
```

The suspicious word was `optimal`. You eventually found the exact bad line:

```text
session optimal pam_motd.so noupdate =/etc/ssh/motd
```

and fixed it to:

```text
session optional pam_motd.so
```

That was the right final move.

But here is what you need to change in future labs.

Do not try to “get through” the lab. That is the wrong goal.

Your goal is to build a habit:

```text
observe → quote evidence → classify → test one assumption → change one thing → verify
```

Before running a command, write:

```text
I am running this command to test:
If I am right, I expect:
If I am wrong, I expect:
```

After running it, write:

```text
Exact output:
What this proves:
What this does not prove:
Next narrow question:
```

Never write only:

```text
worked
didn't work
probably
obviously
I don't know
```

Those are not evidence.

Use this shorter Toyota-style loop:

```text
1. Go see the real thing.
2. Find the exact abnormal condition.
3. Ask why, but answer with evidence.
4. Change one thing.
5. Verify from the logs.
6. Write the lesson so you do not repeat the mistake.
```

For technical work, “go see” means:

```text
read the exact log line
inspect the exact file
compare the exact line
test the exact hypothesis
```

Your future lab reports should be shorter but sharper.

Use this format:

```text
Symptom:
Strongest evidence:
Failure layer:
Wrong assumptions ruled out:
Hypothesis:
Command used to test it:
Result:
Smallest change:
Verification:
Lesson learned:
```

Example from this SSH incident:

```text
Symptom:
SSH connects, accepts password, then closes.

Strongest evidence:
Accepted password for deploy...
pam_open_session(): Permission denied
pam_parse: [...optimal]

Failure layer:
PAM session setup.

Wrong assumptions ruled out:
Firewall, wrong IP, sshd down, wrong password.

Hypothesis:
A malformed PAM session line is breaking sshd login.

Command used to test it:
grep -Rni 'optimal\|pam_motd' /etc/pam.d

Result:
Bad line found in /etc/pam.d/sshd.

Smallest change:
Change `optimal` to `optional` and remove invalid pam_motd arguments.

Verification:
SSH login works and the PAM warnings disappeared.

Lesson learned:
Do not classify a problem before reading the exact log line.
```

That is the standard.

You do not need to become fast yet. You need to become accurate. Speed comes later.

Slow is fine. Careless is not.