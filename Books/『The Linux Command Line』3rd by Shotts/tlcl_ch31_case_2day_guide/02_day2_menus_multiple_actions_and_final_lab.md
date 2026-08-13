# Day 2: Menus, Multiple Actions, and Final Lab
# Exercise 1: Multiple patterns for the same action

## Do not type yet

Decide what inputs should count as yes and no:

```text
yes inputs:
no inputs:
invalid inputs:
```

## Create the script

```bash
cd ~/tlcl-ch31-case

cat > yes-no-case.sh <<'EOF'
#!/usr/bin/env bash

read -r -p "Continue? [y/n] " answer

case "$answer" in
    y|Y|yes|YES)
        echo "Continuing..."
        ;;
    n|N|no|NO)
        echo "Stopping."
        ;;
    *)
        echo "Please answer yes or no."
        ;;
esac
EOF

bash -n yes-no-case.sh
```

Test:

```text
y
Y
yes
YES
n
NO
maybe
empty input
```

## Explain-back

Answer:

1. What does `y|Y|yes|YES)` mean?
2. Why does this make the script friendlier?
3. Is `|` acting like a pipeline here?
4. Why is the default branch still necessary?

---

# Exercise 2: Use `case` with functions
## Do not type yet

Before typing, define the functions in English:

```text
show_system_info:
show_disk_space:
show_home_usage:
show_menu:
```

## Create the script

```bash
cat > report-menu.sh <<'EOF'
#!/usr/bin/env bash

show_system_info () {
    echo "== System Information =="
    hostname
    date
    uname -a
}

show_disk_space () {
    echo "== Disk Space =="
    df -h
}

show_home_usage () {
    echo "== Home Usage =="
    du -sh "$HOME" 2>/dev/null
}

show_menu () {
    echo
    echo "1) System information"
    echo "2) Disk space"
    echo "3) Home usage"
    echo "q) Quit"
    echo
}

show_menu
read -r -p "Choice: " choice

case "$choice" in
    1|system)
        show_system_info
        ;;
    2|disk)
        show_disk_space
        ;;
    3|home)
        show_home_usage
        ;;
    q|Q|quit|exit)
        echo "Goodbye."
        ;;
    *)
        echo "Invalid choice: $choice"
        ;;
esac
EOF

bash -n report-menu.sh
```

Run it several times:

```bash
bash report-menu.sh
```

Test:

```text
1
system
2
disk
3
home
q
quit
bad
empty input
```

## Explain-back

Answer:

1. Which part displays the menu?
2. Which part reads the input?
3. Which part chooses the branch?
4. Which part performs the work?
5. Why is this cleaner than putting all commands directly in the `case` branches?

---

# Exercise 3: Add a loop around the menu

## Skill being gained

He is learning how Chapter 31 combines with Chapter 29.

## Read before exercise

Review the Chapter 29 idea:

```text
while repeats while a condition is true.
```

## Do not type yet

Predict the control flow:

```text
Show menu
Read choice
case chooses action
If q, break
Otherwise repeat
```

## Create a looped version

```bash
cat > report-menu-loop.sh <<'EOF'
#!/usr/bin/env bash

show_system_info () {
    echo "== System Information =="
    hostname
    date
    uname -a
}

show_disk_space () {
    echo "== Disk Space =="
    df -h
}

show_home_usage () {
    echo "== Home Usage =="
    du -sh "$HOME" 2>/dev/null
}

show_menu () {
    echo
    echo "1) System information"
    echo "2) Disk space"
    echo "3) Home usage"
    echo "q) Quit"
    echo
}

while true; do
    show_menu
    read -r -p "Choice: " choice

    case "$choice" in
        1|system)
            show_system_info
            ;;
        2|disk)
            show_disk_space
            ;;
        3|home)
            show_home_usage
            ;;
        q|Q|quit|exit)
            echo "Goodbye."
            break
            ;;
        *)
            echo "Invalid choice: $choice"
            ;;
    esac
done
EOF

bash -n report-menu-loop.sh
bash report-menu-loop.sh
```

## Explain-back

Answer:

1. Why does `while true` repeat forever?
2. What stops the loop?
3. Why does `break` belong in the quit branch?
4. What happens after an invalid choice?
5. How does the script recover from bad input?

---

# Exercise 4: Final debugging discipline

## Skill being gained

He is learning to test all branches, not just the happy path.

## Test table

Fill this in while testing `report-menu-loop.sh`:

| Input | Expected branch | Actual result | Pass/fail | Notes |
|---|---|---|---|---|
| `1` | system info | | | |
| `system` | system info | | | |
| `2` | disk space | | | |
| `disk` | disk space | | | |
| `3` | home usage | | | |
| `home` | home usage | | | |
| `q` | quit | | | |
| `quit` | quit | | | |
| empty input | invalid | | | |
| `99` | invalid | | | |

## Debugging commands

Use Chapter 30 tools:

```bash
bash -n report-menu-loop.sh
bash -x report-menu-loop.sh
```

When using `bash -x`, do not just stare at the noise. Ask:

```text
What line is Bash executing now?
What value does choice contain?
Which case branch did it enter?
```

---

# Final concept questions

Answer in writing:

1. What is the general syntax of a `case` statement?
2. What does `esac` close?
3. What does `;;` end?
4. What does the `*` branch usually mean?
5. Are `case` patterns the same as regular expressions?
6. How can one branch match several choices?
7. Why should more specific patterns usually come before more general patterns?
8. Why is `case` good for menus?
9. Why is it often better for a `case` branch to call a function?
10. How did Chapter 31 build on Chapters 26, 28, 29, and 30?

---

# Final Feynman explain-back

Explain Chapter 31 to a younger student:

```text
A case statement looks at one value and compares it against several patterns.
Each branch says, "If the value looks like this, run these commands."
The first matching branch wins.
The *) branch catches anything unexpected.
This is useful for menus because the user chooses one option from a known list.
```

Then explain the final script:

```text
show_menu prints the choices.
read stores the user's choice.
case checks the choice.
Each branch calls a function.
break exits the loop when the user chooses quit.
```

---

# Day 2 finish standard

He is done with Chapter 31 only if he can write from memory:

```bash
case "$choice" in
    pattern1)
        command
        ;;
    pattern2|pattern3)
        command
        ;;
    *)
        command
        ;;
esac
```

And explain:

```text
This chooses one branch based on shell-style patterns.
It is especially useful for menus and command dispatch.
```
