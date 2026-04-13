# SAMAD - Brother of chmod

## *Because remembering chmod 777 is harder than your final exams.*

---

## Part 1: Linux Permissions - The Thing Everyone Pretends to Understand

### The Cold, Hard Truth

Linux permissions are not difficult. Yet here you are, reading documentation instead of working. Let's fix that in the next 60 seconds.


### Every file has three permission sets:

| Symbol | Means | Can they... |
|--------|-------|--------------|
| `r` | Read | Look at the file? |
| `w` | Write | Mess it up? |
| `x` | Execute | Run it (if it's a program)? |

### The Official Linux Permission Change Syntax:

```bash
chmod [who][action][what] file

who:    u = user (you), g = group (team), o = others (strangers), a = everyone
action: + = add, - = remove, = = set exactly
what:   r = read, w = write, x = execute
```

**Examples the internet shows you:**
```bash
chmod u+x script.sh     # Add execute for user
chmod g-w file.txt      # Remove write from group
chmod a=r file.txt      # Make everyone read-only
```

### The Numeric Shortcut (Octal Mode):

| Number | Permission |
|--------|------------|
| 7 | rwx (read, write, execute) |
| 6 | rw- (read, write) |
| 5 | r-x (read, execute) |
| 4 | r-- (read only) |
| 3 | -wx (write, execute) |
| 2 | -w- (write only) |
| 1 | --x (execute only) |
| 0 | --- (nothing) |

**Example:** `chmod 754 file.txt` means:
- User (7) = rwx
- Group (5) = r-x
- Others (4) = r--

---

## Part 2: You're Still a Noob (And That's Fine)

Let's be honest about what just happened:

- You read that section and thought "I'll remember this" - You won't.
- You've already typed `chmod 777` at least once this month - We know.
- You still Google "how to make file executable linux" - The search history doesn't lie.
- You have no idea what `chmod 2755` means - Nobody does. That's a myth.

**The Statistics (Made up, but believable):**

| Experience Level | Can explain `chmod u+x` | Has broken permissions at 2 AM |
|-----------------|-------------------------|-------------------------------|
| Junior | "Something about users?" | 47 times |
| Mid-level | "Add execute... I think?" | 23 times |
| Senior | "Yes, but let me check Stack Overflow first" | "That one time in 2017..." |
| Architect | "Let's use infrastructure as code instead" | Refuses to answer |

**Here's the truth:** Linux permissions are simple in concept but annoying in practice. You should not need a cheat sheet to change who can read a file.

---

## Part 3: Introducing SAMAD

### What is SAMAD?

**SAMAD** is a command-line tool that translates plain English into correct Linux permission commands. It is the documentation you never read, the Stack Overflow answer you keep searching for, and the colleague who whispers the right syntax - all wrapped in a Python script.

### The Value Proposition

| Without SAMAD | With SAMAD |
|---------------|------------|
| `chmod u+x,g+rx,o-rwx deploy.sh` | `samad give user,group permission to execute deploy.sh` |
| `chown -R apache:www-data /var/www/` | `samad change owner to apache /var/www/ -recursive` |
| `chmod 750 private_data/` | `samad set user permission to read,write,execute and set group permission to read,execute private_data/` |
| "Wait, did I just lock myself out?" | "Let me preview first with default dry-run mode" |

### The Core Philosophy

**Preview by default, execute on request.** SAMAD shows you what it *would* do. You add `-execute` when you are ready.

---

## Part 4: SAMAD Command Reference

### Installation

```bash
# Make script executable
python3 samad give user permission to execute samad -execute

# Optional: install globally
sudo cp samad /usr/local/bin/samad
```

### Basic Syntax

```bash
samad <action> <target> permission to <permission> <file/folder> [options]
```

### Action Keywords

| You type | Linux gets | When to use |
|----------|------------|-------------|
| `give` | `+` | Adding permissions you forgot |
| `remove` | `-` | Removing permissions you regret giving |
| `set` | `=` | Starting fresh (exact permissions only) |

### Target Keywords

| You type | Translation | Real talk |
|----------|-------------|-----------|
| `user` | `u` | Just you |
| `group` | `g` | Your team |
| `others` | `o` | Literal strangers |
| `everyone` / `all` | `a` | YOLO mode |

### Permission Keywords

| You type | Linux gets |
|----------|------------|
| `read` | `r` |
| `write` | `w` |
| `execute` | `x` |

**Pro tip:** Use commas for multiple permissions: `read,write,execute`

---

## Part 5: Practical Examples (The Ones You'll Actually Use)

### Daily Operations

```bash
# Why will not this script run?
samad give user permission to execute deploy.sh

# I need my team to edit this file
samad give group permission to write config.ini

# Stop strangers from seeing my salary data
samad remove others permission to read payroll.xlsx

# Everyone can read this public document
samad set everyone permission to read README.md
```

### Advanced Operations

```bash
# Multiple changes, one command
samad give user permission to read,write and remove group permission to execute app.py

# Recursive folder operations
samad give user,group permission to execute /opt/application/ -recursive

# Change ownership
samad change owner to www-data index.html
samad change group to developers src/ -recursive
```

### The "Show Me What's Happening" Command

```bash
samad show suspicious_file.sh
```

Output:
```text
File: suspicious_file.sh
Permissions: -rwxr-xr--

Explanation:
User  : rwx -> read write execute
Group : r-x -> read execute
Others: r-- -> read
```

No more squinting at `ls -l` output.

---

## Part 6: Options You'll Actually Need

| Option | What it does | When to use it |
|--------|--------------|----------------|
| (nothing) | Preview mode | Always, unless you are 100% sure |
| `-execute` | Actually runs the command | After reviewing the preview |
| `-recursive` | Applies to everything inside folders | When you mean business |
| `-h`, `--help`, `help` | Shows help | Every time you forget the syntax |

**Safety tip:** SAMAD defaults to preview mode. You must explicitly say `-execute` to change anything.

---

## Part 7: The "And" Feature (Because You're Busy)

SAMAD understands the word `and`. Use it to combine multiple permission changes:

```bash
samad give user permission to read,write and remove group permission to execute app.py
```

This generates: `chmod u+rw,g-x app.py`

Two changes. One sentence. Zero headaches.

---

## Part 8: Common Mistakes (And How SAMAD Helps)

| Your natural instinct | What would happen | What SAMAD does instead |
|-----------------------|-------------------|-------------------------|
| `chmod 777 everything` | You just gave the world access | `samad set everyone permission to read,write,execute file` - still dangerous, but now explicit |
| `chmod -R 755 .` | You recursively changed everything | `samad set user,group permission to read,write,execute . -recursive` |
| `sudo chown alice:alice /` | You do not own root anymore | SAMAD will preview before you execute |

---

## Part 9: When NOT to Use SAMAD

1. In production deployment scripts - use direct `chmod` and `chown` commands.
2. If you need advanced permission bits (SUID/SGID/sticky bit).
3. On systems without Python 3.
4. When filenames are exactly permission keywords such as `read` or `write`.

---

## Part 10: Performance & Limitations

| Aspect | Details |
|--------|---------|
| **Speed** | Instant command generation |
| **Memory** | Lightweight |
| **Dependencies** | Python |
| **File limit** | One file/folder per command |
| **Permission complexity** | Basic r/w/x only |
| **Language support** | English |

---

## Part 11: Exit Codes (For Scripting)

| Code | Meaning | What you should do |
|------|---------|---------------------|
| `0` | Success | Continue |
| `1` | Invalid syntax | Read the error message |
| `2` | File not found | Check file path |

---

## Part 12: Quick Reference Card

```bash
# Permission changes
samad give <target> permission to <perms> <file>
samad remove <target> permission to <perms> <file>
samad set <target> permission to <perms> <file>

# Ownership
samad change owner to <username> <file>
samad change group to <groupname> <file>

# Inspection
samad show <file>

# Options
-execute      # Actually do it
-recursive    # Include subdirectories
```

---

## Part 13: The Future (Roadmap)

| Feature | Status |
|---------|--------|
| Numeric mode (`chmod 755`) | Planned |
| Sticky bit support | Maybe |
| Undo functionality | Planned |
| AI-powered permission suggestions | Optional |
| Making coffee | Never |

---

## Part 14: FAQ

**Q: Why is it called SAMAD?**
A: Because it is short, memorable, and less painful than typing chmod syntax from memory.

**Q: Is this production-ready?**
A: Great for personal/admin usage. For CI/CD, prefer explicit shell commands.

**Q: Can I use this with sudo?**
A: Yes. Example: `sudo samad change owner to root file.txt -execute`.

**Q: I typed everything correctly and it still says "Invalid input"**
A: Ensure you include `permission to` for permission-change sentences.

**Q: This is just a wrapper around chmod. Why care?**
A: That is exactly the point. Better readability and safer default preview mode.

---

## Part 15: License & Warranty

**License:** Use freely for personal and educational purposes.

**Warranty:** None. Always review preview output before adding `-execute`.

---

## Final Words

You now have two options:

1. Keep memorizing octal tables and re-Googling chmod syntax.
2. Use SAMAD and move on with your actual work.

```bash
# Start here
python3 samad help
```

Now go fix those permissions.

---

*SAMAD: Because `chmod u+x` is not intuitive, and pretending it is helps nobody.*
