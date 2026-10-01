# Unix Terminal Session

Follow along live: copy each command, paste it into your terminal, press **Enter**.
You will create all files and folders yourself, right from the terminal.

## 0. Open your terminal

- **Windows:** open **Ubuntu** from the Start menu (this is WSL)
- **macOS:** open the **Terminal** app
- **Linux:** open your **Terminal**

> Tip: Zoom in (Ctrl/Cmd and +) so everything is easy to read.
> Pasting: Ctrl+Shift+V (Linux/WSL) or Cmd+V (macOS). In Windows Ubuntu, right-click also pastes.

---

## 1. Terminal basics

Where am I?

```
pwd
```

What is in this folder?

```
ls
```

Create a folder for today and go into it:

```
mkdir demo
```

```
cd demo
```

Check that you are inside `demo`:

```
pwd
```

### Two special symbols: `/` and `~`

**`/` (slash)** has two jobs:

- A single `/` is the **root**: the very top folder of the whole computer. Everything else lives below it.
- Inside a path, `/` separates folders: `/home/fresenius/demo` means "root → home → fresenius → demo".

Go to the top and look around:

```
cd /
```

```
ls
```

**`~` (tilde)** is a shortcut for **your home folder**, the place where your own files live. It saves you from typing the full path.

Jump back home from anywhere:

```
cd ~
```

```
pwd
```

Look into your home folder without leaving where you are:

```
ls ~
```

Go back into the demo folder:

```
cd demo
```

> Tip: `cd` without anything after it also takes you home.

---

## 2. Small, modular tools

> **The Unix idea:** every tool does one thing well. You combine tools with the pipe `|`.

### What is piping?

Every command produces **output** (text on your screen). The pipe `|` takes the output of the command on its left and hands it as **input** to the command on its right.

Think of an assembly line: each station does one small job and passes the result to the next station.

```
cat names.txt  |  sort  |  uniq
 (show file)    (order)   (remove duplicates)
```

Related: the symbol `>` does not send output to another command, but **into a file**. You already use it below to create files.

### Create a file

This creates `names.txt` with 15 names (some appear more than once):

```
printf "Anna\nBen\nClara\nAnna\nDavid\nBen\nEva\nAnna\nFelix\nClara\nGreta\nBen\nHans\nEva\nIvo\n" > names.txt
```

Show the file:

```
cat names.txt
```

### Build a pipeline step by step

Sort the names alphabetically:

```
cat names.txt | sort
```

Remove duplicates (`uniq` only works on sorted input):

```
cat names.txt | sort | uniq
```

Count the unique names:

```
cat names.txt | sort | uniq | wc -l
```

You should see `9`.

### Filter with grep

Show only lines containing "Anna":

```
cat names.txt | grep "Anna"
```

How often does "Anna" appear?

```
cat names.txt | grep "Anna" | wc -l
```

You should see `3`.

### Count files with a pipe

Create three more files:

```
touch one.txt two.txt three.txt
```

Count the files in this folder:

```
ls | wc -l
```

**Key idea:** `cat`, `sort`, `uniq`, `grep` and `wc` know nothing about each other, but together they solve new problems.

---

## 3. Unix is multi-user

Who am I?

```
whoami
```

Who is logged in right now?

```
who
```

Which users exist on this computer?

```
cat /etc/passwd
```

Show your files with owner and permissions:

```
ls -l
```

How to read a line like `-rw-r--r-- 1 fresenius fresenius 60 Oct 2 10:00 names.txt`:

| Part | Meaning |
|---|---|
| `-rw-r--r--` | permissions (r = read, w = write, x = execute) for owner, group, everyone else |
| `fresenius fresenius` | owner and group of the file |
| `names.txt` | file name |


`Permission denied`. This is how Unix protects users from each other.

> **macOS users:** this file does not exist on your system. Use `cat /etc/master.passwd` instead and `sudo cat /etc/master.passwd` below.

### `sudo`: borrow admin rights for one command

Every Unix system has a special all-powerful user called **root** (the administrator). Normal users are not allowed to change the system or read other people's private files.

`sudo` means "**s**uper**u**ser **do**": it runs **one single command** with root rights, after you type your password.

Who am I as root?

```
sudo whoami
```

Your password is requested. **Nothing appears while you type it, that is normal.** The answer is `root`.

Now read the protected file:

```
sudo cat /etc/shadow
```

This time it works.

> **Careful:** with `sudo` there is no safety net. Never run `sudo` commands you copied from the internet without understanding them. For example, `sudo rm` can delete system files.

---

## 4. Mini task

Create this file:

```
printf "apple\nbanana\napple\ncherry\nbanana\napple\n" > fruits.txt
```

**Goal:** Find out how many *different* fruits are in `fruits.txt`.
Use the tools from section 2.

<details>
<summary>Solution</summary>

```
cat fruits.txt | sort | uniq | wc -l
```

Result: `3`

</details>

---

## 5. Clean up

Leave the folder and delete it with everything inside:

```
cd ..
```

```
rm -r demo
```

---

## Cheat sheet

| Command | What it does |
|---|---|
| `pwd` | show current folder |
| `ls` / `ls -l` | list files / with details |
| `cd` | change folder |
| `mkdir` | create folder |
| `touch` | create empty file |
| `cat` | show file content |
| `\|` | pipe: send output of one tool to the next |
| `>` | write output into a file |
| `/` | root folder (top of the system) and separator in paths |
| `~` | shortcut for your home folder |
| `..` | the folder one level above (`cd ..` = go up one level) |
| `sudo` | run one command with admin (root) rights |
| `whoami` / `who` | current user / logged-in users |
| `chmod` | change permissions |
| `rm` | delete (careful: no recycle bin!) |
