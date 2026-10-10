# Build and Publish Your Own Website with GitHub Pages

In this tutorial you will build a small website and put it on the internet, so that anyone in the world can open it with a link like `https://yourusername.github.io`.

You do **not** need any prior knowledge of web development or Git. We go step by step, and we explain every command. Claude (the AI assistant) will write the website code for you. Your job is to describe what you want, to understand the workflow, and to publish the result.

---

## What you will learn

- What **Git** and **GitHub** are and why almost every software project uses them
- How to use the terminal to install software and work with files
- How to let an AI write a simple website for you
- How to save versions of your work (**commit**) and upload them (**push**)
- How to publish a website for free with **GitHub Pages**

## The big picture

```
 Your computer                                   GitHub (the internet)
 ┌──────────────────────────┐                   ┌─────────────────────────┐
 │  index.html              │   git push        │  Repository             │
 │  (you + Claude create it)│ ────────────────▶ │  yourusername.github.io │
 └──────────────────────────┘                   └────────────┬────────────┘
                                                             │ GitHub Pages
                                                             ▼
                                              https://yourusername.github.io
                                              (visible for everyone)
```

## Words you need to know

| Word | Simple explanation |
|------|--------------------|
| **Repository** ("repo") | A project folder that Git keeps track of, including its complete history of changes. |
| **Git** | A program on your computer that records the history of your files. Think of it as "save points in a video game". |
| **GitHub** | A website that stores repositories online. It is a backup, a way to share code, and (with GitHub Pages) a free web host. |
| **Clone** | Download a copy of an online repository to your computer. |
| **Commit** | A saved snapshot of your project at one moment, with a short description. |
| **Push** | Upload your commits from your computer to GitHub. |
| **HTML** | The language that web pages are written in. A website starts with a file called `index.html`. |
| **Terminal** | The text window where you type commands. You have already seen it in class. |

> **Important:** Everything you put in a *public* repository is visible to everyone on the internet. Do not publish your home address, phone number, private photos, passwords, or anything you would not want strangers to see.

---

# Part 1: Your first website online

## Step 1: Create the repository on GitHub

This is done in the browser, not in the terminal.

1. Open [github.com](https://github.com) and log in.
2. Click the **`+`** symbol in the top right corner and choose **New repository**.
3. Under **Repository name**, type exactly:

   ```
   yourusername.github.io
   ```

   Replace `yourusername` with your real GitHub username, **in lowercase letters**. If your username is `MaxMueller`, the name is `maxmueller.github.io`. The name has to match exactly, otherwise GitHub will not treat it as your personal website.
4. Select **Public** as visibility. (Free GitHub Pages only works with public repositories.)
5. Tick the box **Add a README file**. This creates a first file, which makes the next steps easier.
6. Click **Create repository**.

You now have an (almost empty) project online. Next, we get it onto your own computer.

---

## Step 2: Install Git

Open your terminal.

- **Mac:** press `Cmd + Space`, type `Terminal`, press Enter.
- **Linux (Ubuntu):** press `Ctrl + Alt + T` (or search for "Terminal" in your applications).

First, check whether Git is already installed:

```bash
git --version
```

If you see something like `git version 2.43.0`, Git is installed and you can skip to Step 3. If you see "command not found", install it:

### Mac

We use **Homebrew**, a tool that installs software from the terminal. Check if you have it:

```bash
brew --version
```

If that fails, install Homebrew (copy the whole line, paste it in the terminal, press Enter, and follow the instructions; it will ask for your Mac password, and you will not see the characters while typing, which is normal):

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

At the end of the installation, Homebrew prints a section called **Next steps**. Run the commands it shows you, then close and reopen the terminal.

Now install Git:

```bash
brew install git
```

### Linux (Ubuntu)

In this course we use **Ubuntu**. Install Git with:

```bash
sudo apt update
sudo apt install git
```

> `sudo` means "run this as administrator". The terminal asks for your password. You will not see any characters while typing it. That is normal. Just type it and press Enter.

### Check that it worked

```bash
git --version
```

The command should print a version number.

---

## Step 3: Tell Git who you are

Every commit (save point) records who made it. You only need to do this **once per computer**.

```bash
git config --global user.name "Your Name"
git config --global user.email "your-email@example.com"
```

Replace the text in quotes with your own name and email address.

**Tip for privacy:** your email address will be visible in public commits. GitHub offers a hidden address instead. Go to GitHub → **Settings → Emails**, tick **Keep my email addresses private**, and use the address shown there (it looks like `12345678+yourusername@users.noreply.github.com`) in the command above.

Check your settings:

```bash
git config --global --list
```

---

## Step 4: Create an access token so that Git can upload to GitHub

You use GitHub in the **browser**. Only Git itself runs in the terminal. But when you upload your work from the terminal (Step 9), GitHub has to be sure that it is really you. GitHub no longer accepts your normal password for this. Instead, you create a **personal access token**: a long, randomly generated password that is used only for this purpose. You create it in the browser:

1. On github.com, click your **profile picture** (top right) and choose **Settings**.
2. In the left sidebar, scroll all the way down and click **Developer settings**.
3. Click **Personal access tokens**, then **Tokens (classic)**.
4. Click **Generate new token**, then **Generate new token (classic)**. GitHub may ask for your password again.
5. Fill in the form:
   - **Note:** a name for yourself, for example `seminar laptop`
   - **Expiration:** `90 days` is fine
   - **Select scopes:** tick **`repo`** (and nothing else)
6. Click **Generate token** at the bottom of the page.
7. **Copy the token immediately** (it starts with `ghp_`). GitHub shows it **only once**. Keep it somewhere safe for the next few minutes, for example in a password manager. If you lose it, you simply create a new one.

> **Treat the token like a password.** Never put it in a file of your project (especially not in `index.html`, which is public!), never send it to anyone, and never paste it into a chat.

Now tell Git to remember your login, so that you do not have to type the token every time:

**Mac:**

```bash
git config --global credential.helper osxkeychain
```

**Linux (Ubuntu):**

```bash
git config --global credential.helper store
```

(On Linux, `store` saves the token in a plain text file in your home folder. That is fine on your own laptop, but do not use it on a shared computer.)

You will enter the token for the first time in Step 9, when you upload your website.

---

## Step 5: Clone the repository to your computer

"Cloning" downloads your online repository to your computer. First we create a folder for all your projects and move into it.

```bash
mkdir -p ~/projects
cd ~/projects
```

What these commands do:

- `mkdir -p ~/projects` creates a folder called `projects` in your home directory (`~` is a shortcut for your home directory). The `-p` means "no error if it already exists".
- `cd ~/projects` ("change directory") moves the terminal into that folder.

Now clone. Replace `yourusername` with your GitHub username:

```bash
git clone https://github.com/yourusername/yourusername.github.io.git
```

Git downloads the repository and creates a new folder with the same name. Move into it:

```bash
cd yourusername.github.io
```

Look at what is inside:

```bash
ls -la
```

You should see:

- `README.md`: the file GitHub created for you
- `.git`: a **hidden folder** (it starts with a dot) where Git stores the entire history of your project. **Never delete or edit this folder by hand.**

> **Always make sure you are in the right folder before running Git commands.** The command `pwd` ("print working directory") shows where you are. It should end with `yourusername.github.io`.

---

## Step 6: Let Claude write your index.html

A website always starts with a file named **`index.html`**. It is the page that opens when someone visits your address. It must be named exactly like that, all lowercase.

### 6.1 Ask Claude

Open [claude.ai](https://claude.ai) and describe your website. The more specific you are, the better the result. Here is an example prompt you can adapt:

```
Please create a personal website as a single index.html file.

About me: My name is [name], I study computer science in my first semester
at [university]. My hobbies are [hobby 1] and [hobby 2].

Requirements:
- Everything in one file (HTML and CSS together, no external libraries)
- A header with my name, a short "About me" section, and a section with my hobbies
- Clean, modern design with a calm color scheme
- Must look good on both a laptop and a smartphone
- Include the viewport meta tag and UTF-8 encoding

Give me the complete file so that I can copy and paste it.
```

Do not include private data (address, phone number) in your prompt, because it would end up on the public internet.

### 6.2 Save the code as a file

Claude gives you a block of code, which starts with `<!DOCTYPE html>`. Copy all of it. Then, in your terminal (make sure you are in the `yourusername.github.io` folder), create the file with the text editor **nano**:

```bash
nano index.html
```

An editor opens inside the terminal.

1. **Paste** the code:
   - Mac: `Cmd + V`
   - Linux (Ubuntu): `Ctrl + Shift + V`
2. **Save:** press `Ctrl + O`, then Enter to confirm the file name.
3. **Exit:** press `Ctrl + X`.

(nano is not pretty, but it is installed everywhere. If you prefer a graphical editor, you can use [Visual Studio Code](https://code.visualstudio.com), which is free. Any plain text editor works, but not Word.)

Check that the file exists:

```bash
ls
```

---

## Step 7: Look at your website on your own computer

You do not need the internet to see your website. Open the file directly in your browser:

**Mac:**

```bash
open index.html
```

**Linux (Ubuntu):**

```bash
xdg-open index.html
```

Your browser opens and shows your page. Not happy with it? Great, now you can improve it:

1. Go back to Claude and write what you want to change ("Make the header dark blue", "Add a section about my favorite books", "Make the text bigger").
2. Copy the new code, run `nano index.html` again, **delete the old content** (in nano: hold `Ctrl + K` to cut line by line), paste the new code, save and exit.
3. Refresh the browser tab (`Cmd + R` on Mac, `F5` on Linux).

Repeat until you are happy. **Nothing is online yet.** This is still only on your computer. That is the point: you can experiment safely.

---

## Step 8: Save a version with Git (add and commit)

Now we use Git for the first time. Read this section carefully, because it explains the idea behind Git.

### 8.1 The idea behind Git

Imagine you are writing an important essay. You save "essay_v1", then "essay_v2", then "essay_final", "essay_final_REALLY_final"... Git solves this problem in a clean way. It keeps **one** project and remembers every saved version, so you can always go back.

Git works with **three places** on your computer, plus GitHub online:

```
 1. Working folder        2. Staging area          3. Local repository       4. GitHub
 (your files, where       (the "waiting room":     (the history of           (online copy)
  you edit)                what goes into the       all commits)
                           next commit)

    index.html  ──git add──▶   index.html  ──git commit──▶  Commit #2  ──git push──▶  Commit #2
                                                             Commit #1                 Commit #1
```

An everyday comparison is sending a package:

| Git command | Package comparison |
|-------------|--------------------|
| Edit files | You collect items on the table. |
| `git add` | You put the items you want to send **into the box**. |
| `git commit` | You **seal the box and write a label** on it ("what is inside"). |
| `git push` | You **hand the box to the post office** (GitHub). |

Why the extra "staging" step? It lets you choose *which* changes belong together in one commit. For now, we simply add everything.

### 8.2 Ask Git what is going on: `git status`

```bash
git status
```

This is the **most useful Git command**. Whenever you are unsure, run it. You should see something like:

```
On branch main
Your branch is up to date with 'origin/main'.

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        index.html
```

"Untracked" means: the file exists in your folder, but Git does not know about it yet. (`main` is the name of your main line of development, called a *branch*.)

### 8.3 Put the file in the box: `git add`

```bash
git add index.html
```

Nothing is printed, which is normal. Run `git status` again:

```bash
git status
```

Now the file is listed under **"Changes to be committed"**. It is in the staging area and ready to be saved.

> **Shortcut for later:** `git add .` adds *all* new and changed files in the current folder (the dot means "here"). Use it when you want to include everything, but check with `git status` first so that you do not add something by accident.

### 8.4 Seal the box: `git commit`

```bash
git commit -m "Add first version of my website"
```

- `commit` creates the snapshot.
- `-m` stands for "message". The text in quotes describes **what you changed**.

Good commit messages are short and describe the change, for example "Add hobbies section" or "Change color scheme to dark blue". Bad messages are "stuff" or "asdf", because in three months you will not know what they mean.

Look at your history:

```bash
git log --oneline
```

You see a list of your commits, newest first, each with a short ID and your message. You should see your new commit on top of the first one GitHub created ("Initial commit").

> **Remember:** a commit is saved only **on your computer**. GitHub does not know about it yet. That is the next step.

---

## Step 9: Upload to GitHub with `git push`

```bash
git push
```

The first time, Git asks for your login:

```
Username for 'https://github.com':
Password for 'https://yourusername@github.com':
```

- **Username:** type your GitHub username and press Enter.
- **Password:** paste your **token** from Step 4 (**not** your normal GitHub password) and press Enter. You will not see any characters while pasting, which is normal.

Git remembers the token from now on. Then Git uploads your commit. You will see some lines of progress, ending with something like `main -> main`.

Now go to your repository in the browser: `https://github.com/yourusername/yourusername.github.io`. You should see `index.html` in the file list, with your commit message next to it.

**If you see an error:**

- `Authentication failed`: check that you pasted the **token** (not your GitHub password), that it has the `repo` scope, and that it has not expired. If in doubt, create a new token (Step 4).
- `rejected ... fetch first`: your online repository has changes that you do not have yet. Run `git pull` and then `git push` again.

---

## Step 10: See your website online

GitHub Pages usually turns on automatically for repositories named `username.github.io`. To make sure:

1. In your repository on GitHub, click **Settings** (top row of tabs).
2. In the left sidebar, click **Pages**.
3. Under **Build and deployment → Source**, it should say **Deploy from a branch**, with branch **`main`** and folder **`/ (root)`**. If not, select these options and click **Save**.

GitHub now "builds" your site. This takes **1 to 2 minutes**. You can watch the progress in the **Actions** tab of your repository: a yellow dot means "working", a green check mark means "done".

Then open your website:

```
https://yourusername.github.io
```

**Congratulations, your website is online!** Send the link to a friend, and open it on your smartphone to check that it looks good there as well.

> If you still see the old version or a 404 page: wait one more minute, then do a hard refresh (`Cmd + Shift + R` on Mac, `Ctrl + Shift + R` on Linux).

---

## Your workflow from now on

Every time you want to change your website, you repeat the same loop:

```
 1. Change index.html (ask Claude, then edit the file)
 2. Look at it locally        →  open index.html  /  xdg-open index.html
 3. git status                →  what changed?
 4. git add .                 →  put changes in the box
 5. git commit -m "message"   →  seal the box with a label
 6. git push                  →  send it to GitHub
 7. Wait 1-2 minutes, then reload https://yourusername.github.io
```

---

# Part 2: Making your website better

Your website is online. Now you can make it bigger and more beautiful. Work in small steps and **commit after every step that works**. If something breaks, you can always go back to the last good commit.

## 2.1 Make it look better with Claude

Claude can restyle your whole page. Be specific about what you want. Some example prompts, which you paste together with your current `index.html`:

```
Here is my current index.html: [paste the code]
Please change the design: use a dark theme, a modern sans-serif font,
and more spacing between the sections. Keep all my text.
Give me the complete updated file.
```

```
Add a navigation bar at the top with links to my sections.
Make it collapse into a simple menu on small phone screens.
```

**Tips for good results:**

- Always paste your current code, so Claude changes it instead of starting from scratch.
- Ask for **the complete file**, so you can replace the whole content.
- Change **one thing at a time**. Then you can see which change caused a problem.
- Not sure what a part of the code does? Ask Claude: "Explain this code to me as if I were a beginner."
- Test on a phone-sized screen: in Chrome or Firefox press `F12` (Mac: `Cmd + Option + I`) and click the small phone/tablet icon, or simply open your live site on your smartphone.

## 2.2 Add more pages (subpages)

A website can have many pages. Each page is simply another `.html` file in the same folder.

Example structure:

```
yourusername.github.io/
├── index.html        ← home page
├── about.html        ← subpage
├── projects.html     ← subpage
└── images/           ← folder for pictures
```

Ask Claude:

```
Please create an additional page about.html with the same design as my
index.html. Then update my index.html so that it links to about.html,
and add a link back to the home page on about.html.
```

A link in HTML looks like this:

```html
<a href="about.html">About me</a>
```

After the commit and push, the subpage is available at:

```
https://yourusername.github.io/about.html
```

**Remember to add new files to Git:** `git add .` includes them. Check with `git status` that the new files appear under "Changes to be committed".

## 2.3 Add photos

1. **Create a folder for images** inside your project:

   ```bash
   mkdir images
   ```

2. **Copy a photo into it.** For example, a photo from your Downloads folder:

   ```bash
   cp ~/Downloads/me.jpg images/me.jpg
   ```

   (`cp` copies a file: `cp source destination`. On Mac and Linux it is the same command. On Mac, your folder might be called `Downloads` as well; use `ls ~/Downloads` to see the file names.)

3. **Use the image in your HTML.** Ask Claude: "Please add the picture `images/me.jpg` to my About section, round it, and make it responsive." The HTML for an image looks like this:

   ```html
   <img src="images/me.jpg" alt="A photo of me at the beach">
   ```

   The `alt` text describes the picture for people who cannot see it (screen readers) and appears when the image fails to load.

4. **Commit and push** as usual:

   ```bash
   git add .
   git commit -m "Add profile photo"
   git push
   ```

**Rules for images:**

- Use **lowercase file names without spaces**: `me.jpg`, `trip-italy.jpg`. Avoid `My Photo (1).JPG`.
- GitHub Pages is **case-sensitive**: `Photo.jpg` and `photo.jpg` are different files. If an image works on your computer but not online, this is the most common reason.
- Keep images **small** (ideally under 500 KB). Phone photos can be 5 MB or more, which makes your website slow. Ask Claude how to shrink them, or use a free tool like [squoosh.app](https://squoosh.app).
- Only publish photos that you are allowed to publish, and think about the privacy of people in them.

## 2.4 Ideas for more

- A **projects page** where you list things you build during your studies
- A **contact section** (be careful with what personal data you publish)
- A **blog page** where you write about what you learn
- Your own **domain name** (for example `yourname.com`), which is possible with GitHub Pages but not free
- Ask Claude: "What could I add to make my website more interesting for a future employer?"

---

# Troubleshooting

| Problem | Likely cause and solution |
|---------|---------------------------|
| `fatal: not a git repository` | You are in the wrong folder. Run `pwd` to see where you are, then `cd ~/projects/yourusername.github.io`. |
| `nothing to commit, working tree clean` | You have not changed anything since the last commit. Edit and save a file first. |
| `Authentication failed` when pushing | Use the **token** from Step 4 as password, not your GitHub password. Check that it has the `repo` scope and has not expired; otherwise create a new one. |
| `rejected ... fetch first` when pushing | Your online repository has newer content. Run `git pull`, then `git push`. |
| Website shows **404** | Wait 2 minutes. Check that the repository name is exactly `yourusername.github.io` (lowercase), that it is **Public**, that the file is named `index.html`, and that Pages is set to `main` / `(root)` under Settings → Pages. |
| Changes do not show up online | Did you run `git push`? Check the **Actions** tab for a green check mark, then do a hard refresh (`Cmd/Ctrl + Shift + R`). |
| Image does not show | Check the path (`images/me.jpg`), upper/lower case in the file name, and that you ran `git add .` so the image was committed. |
| Terminal shows `command not found: git` | Git is not installed or the terminal was not restarted. Repeat Step 2, close the terminal, and open a new one. |
| nano looks confusing | Shortcuts are listed at the bottom of the screen (`^` means `Ctrl`). Save with `Ctrl + O`, Enter, and quit with `Ctrl + X`. |
| You made a mistake and are stuck | Run `git status` and read the output carefully; Git usually tells you what to do. Or copy the error message to Claude and ask for help. |

---

# Cheat sheet

## Terminal commands

| Command | What it does |
|---------|--------------|
| `pwd` | Show the current folder |
| `ls` / `ls -la` | List files / list all files including hidden ones |
| `cd folder` | Go into a folder |
| `cd ..` | Go one folder up |
| `mkdir name` | Create a new folder |
| `cp source destination` | Copy a file |
| `nano file` | Edit a file in the terminal |
| `open file` (Mac) / `xdg-open file` (Linux) | Open a file with its default program |

## Git commands

| Command | What it does |
|---------|--------------|
| `git clone URL` | Download a repository from GitHub |
| `git status` | Show what changed and what is staged |
| `git add file` / `git add .` | Put a file / all changes in the staging area |
| `git commit -m "message"` | Save a snapshot with a description |
| `git log --oneline` | Show the history of commits |
| `git push` | Upload commits to GitHub |
| `git pull` | Download new commits from GitHub |

**The daily routine:** `git status` → `git add .` → `git commit -m "..."` → `git push`
