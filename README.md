# 📖 The Definitive Guide to Git, GitHub, and Visual Studio Code Integration

> A complete technical handbook detailing workstation environment setup, deep-dive cryptographic authentication protocols (HTTPS, Fine-Grained PATs, SSH Ed25519, and GitHub CLI), repository management, VS Code native integration, and modern version control workflows.

---

## 📑 Table of Contents

1. [Architectural Overview & Core Mechanics](#1-architectural-overview--core-mechanics)
   - [1.1 Git vs. GitHub: Distributed VCS vs. Cloud Collaboration Platform](#11-git-vs-github-distributed-vcs-vs-cloud-collaboration-platform)
   - [1.2 The Three-Tree Architecture: Working Directory, Index, and HEAD](#12-the-three-tree-architecture-working-directory-index-and-head)
   - [1.3 Git Object Model: Commits, Trees, Blobs, and SHA-1/SHA-256 Hashes](#13-git-object-model-commits-trees-blobs-and-sha-1sha-256-hashes)
2. [Workstation Preparation & Global Configuration](#2-workstation-preparation--global-configuration)
   - [2.1 Installing Core Software on Windows](#21-installing-core-software-on-windows)
   - [2.2 Configuring Global Developer Identity](#22-configuring-global-developer-identity)
   - [2.3 Line Ending Sanitization: Windows CRLF vs. Unix LF](#23-line-ending-sanitization-windows-crlf-vs-unix-lf)
   - [2.4 Setting the Default Branch (`main`)](#24-setting-the-default-branch-main)
3. [Deep-Dive Authentication Strategies](#3-deep-dive-authentication-strategies)
   - [3.1 Method A: HTTPS & Personal Access Tokens (PAT)](#31-method-a-https--personal-access-tokens-pat)
     - [3.1.1 Why Traditional Passwords Are Deprecated](#311-why-traditional-passwords-are-deprecated)
     - [3.1.2 Classic Personal Access Tokens (Scope-Based)](#312-classic-personal-access-tokens-scope-based)
     - [3.1.3 Fine-Grained Personal Access Tokens (Least-Privilege Security)](#313-fine-grained-personal-access-tokens-least-privilege-security)
     - [3.1.4 Configuring Git Credential Manager (GCM) on Windows](#314-configuring-git-credential-manager-gcm-on-windows)
   - [3.2 Method B: Secure Shell (SSH) Cryptographic Integration](#32-method-b-secure-shell-ssh-cryptographic-integration)
     - [3.2.1 Asymmetric Cryptography: Public Key vs. Private Key](#321-asymmetric-cryptography-public-key-vs-private-key)
     - [3.2.2 Key Algorithms: Ed25519 vs. RSA 4096-bit](#322-key-algorithms-ed25519-vs-rsa-4096-bit)
     - [3.2.3 Generating the Cryptographic Key Pair](#323-generating-the-cryptographic-key-pair)
     - [3.2.4 Configuring the Windows SSH Agent Service](#324-configuring-the-windows-ssh-agent-service)
     - [3.2.5 Adding the Public Key to Your GitHub Account](#325-adding-the-public-key-to-your-github-account)
     - [3.2.6 Testing and Validating the SSH Handshake](#326-testing-and-validating-the-ssh-handshake)
   - [3.3 Method C: GitHub CLI (`gh`) Web-Based OAuth Flow](#33-method-c-github-cli-gh-web-based-oauth-flow)
     - [3.3.1 Installing and Verifying GitHub CLI](#331-installing-and-verifying-github-cli)
     - [3.3.2 Interactive Authentication via Terminal & Browser](#332-interactive-authentication-via-terminal--browser)
4. [Repository Workflows & Project Bootstrapping](#4-repository-workflows--project-bootstrapping)
   - [4.1 Workflow 1: Clone Remote First (Recommended Standard)](#41-workflow-1-clone-remote-first-recommended-standard)
   - [4.2 Workflow 2: Initialize Locally First & Link to Remote](#42-workflow-2-initialize-locally-first--link-to-remote)
   - [4.3 Inspecting and Modifying Remote URLs](#43-inspecting-and-modifying-remote-urls)
5. [Visual Studio Code Integration](#5-visual-studio-code-integration)
   - [5.1 Launching VS Code via Command Line Interface (CLI)](#51-launching-vs-code-via-command-line-interface-cli)
   - [5.2 Navigating the Source Control Panel (GUI)](#52-navigating-the-source-control-panel-gui)
   - [5.3 Integrated Terminal Configuration & Best Practices](#53-integrated-terminal-configuration--best-practices)
   - [5.4 Essential VS Code Extensions for Git & Markdown](#54-essential-vs-code-extensions-for-git--markdown)
6. [The Core Git Execution Lifecycle: Add, Commit, Push](#6-the-core-git-execution-lifecycle-add-commit-push)
   - [6.1 Staging State Operations (`git add`)](#61-staging-state-operations-git-add)
   - [6.2 Writing Deterministic History Snapshots (`git commit`)](#62-writing-deterministic-history-snapshots-git-commit)
   - [6.3 Remote Upstream Synchronization (`git push`)](#63-remote-upstream-synchronization-git-push)
   - [6.4 Fetching and Pulling Remote Upgrades (`git pull` & Rebase)](#64-fetching-and-pulling-remote-upgrades-git-pull--rebase)
7. [Comprehensive Troubleshooting & Diagnostics](#7-comprehensive-troubleshooting--diagnostics)
   - [7.1 Warning: `LF will be replaced by CRLF`](#71-warning-lf-will-be-replaced-by-crlf)
   - [7.2 Error: `Support for password authentication was removed`](#72-error-support-for-password-authentication-was-removed)
   - [7.3 Error: `Permission denied (publickey)`](#73-error-permission-denied-publickey)
   - [7.4 Error: `fatal: remote origin already exists`](#74-error-fatal-remote-origin-already-exists)
   - [7.5 Error: `Updates were rejected because the remote contains work that you do not have`](#75-error-updates-were-rejected-because-the-remote-contains-work-that-you-do-not-have)
8. [Quick Reference Cheat Sheet](#8-quick-reference-cheat-sheet)

---

## 1. Architectural Overview & Core Mechanics

Understanding Git requires separating the local database running on your physical workstation from the hosted platform living on remote servers.

### 1.1 Git vs. GitHub: Distributed VCS vs. Cloud Collaboration Platform

```
+--------------------------------------------------------------------------+
|                               GIT                                        |
|  * Decentralized / Distributed Version Control System                     |
|  * Works completely offline; local commits, branches, merges            |
|  * Records file delta snapshots directly inside the `.git` directory     |
+--------------------------------------------------------------------------+
                                     |
                         Synchronized via Network
                           (HTTPS, SSH, or CLI)
                                     v
+--------------------------------------------------------------------------+
|                              GITHUB                                      |
|  * Centralized cloud hosting platform for Git repositories               |
|  * Enables asynchronous team collaboration, Pull Requests, Code Reviews  |
|  * Provides CI/CD pipelines (GitHub Actions), Issue Trackers, & Wiki     |
+--------------------------------------------------------------------------+
```

* **Git** is the engine. It tracks incremental file state snapshots locally without needing an internet connection.
* **GitHub** is the platform. It provides a web interface, storage backup, security scans, and peer-review workflows for repositories tracked with Git.

---

### 1.2 The Three-Tree Architecture: Working Directory, Index, and HEAD

A local Git repository is composed of three interconnected stages:

```
[ 1. Working Directory ]  ==== (git add) ====>  [ 2. Staging Area / Index ]
  * Files on disk                                  * Prepared snapshot
  * Untracked / Modified                           * Ready for release
           ^                                                |
           |                                                | (git commit)
           |                                                v
           +================ (git checkout) ====  [ 3. Local Repository / HEAD ]
                                                    * Immutable historical record
                                                    * `.git` metadata store
```

1. **Working Directory:** The local filesystem folder containing your raw project files, images, scripts, and documentation.
2. **Staging Area (Index):** A binary staging cache file (located at `.git/index`) representing the exact state intended for the next commit snapshot.
3. **Local Repository (HEAD):** The internal Git database where every commit hash points to permanent objects stored in `.git/objects`.

---

### 1.3 Git Object Model: Commits, Trees, Blobs, and SHA-1/SHA-256 Hashes

Every element inside Git is an immutable cryptographic object:
* **Blob (Binary Large Object):** Holds the raw content of a file without its filename or directory structure.
* **Tree:** Corresponds to a directory listing. It holds pointers (hashes) to blobs and sub-trees along with filenames and file modes.
* **Commit:** An object containing a pointer to the root tree object, metadata (author, committer, timestamp), a commit message, and references to parent commit hashes.
* **Cryptographic Hashes:** Every object is uniquely identified by a 40-character hexadecimal string calculated using SHA-1 (or modern SHA-256), ensuring absolute data integrity.

---

## 2. Workstation Preparation & Global Configuration

### 2.1 Installing Core Software on Windows

Before executing Git commands, install the latest stable toolchain:

1. **Git for Windows:** Download from [git-scm.com](https://git-scm.com/).
   - Ensure the installer enables **Git Credential Manager** and integrates Git into the Windows Command Prompt and PowerShell.
2. **Visual Studio Code:** Download and install the 64-bit User Installer from [code.visualstudio.com](https://code.visualstudio.com/).
   - During setup, check **"Add to PATH"**, **"Add 'Open with Code' action to Windows Explorer file context menu"**, and **"Add 'Open with Code' action to directory context menu"**.
3. **GitHub CLI (`gh`):** Download the Windows installer (`.msi`) from [cli.github.com](https://cli.github.com/) or install via Windows Package Manager:
   ```cmd
   winget install --id GitHub.cli
   ```

Verify your installations inside Windows Command Prompt (`cmd.exe`):
```cmd
git --version
code --version
gh --version
```

---

### 2.2 Configuring Global Developer Identity

Git signs every commit with an author name and email. These credentials must match your GitHub account settings to associate your contributions with your profile.

Run the following commands in CMD:
```cmd
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

Verify that the global settings are correctly stored in `%USERPROFILE%\.gitconfig`:
```cmd
git config --global --list
```

---

### 2.3 Line Ending Sanitization: Windows CRLF vs. Unix LF

* Windows uses **Carriage Return + Line Feed** (`\r\n` or `CRLF`).
* Unix/Linux and macOS use **Line Feed** (`\n` or `LF`).

To avoid noisy Git diffs and file corruption across cross-platform teams, configure Git to automatically convert line endings on Windows:

```cmd
git config --global core.autocrlf true
```

---

### 2.4 Setting the Default Branch (`main`)

Modern Git and GitHub standards use `main` as the default primary branch name:

```cmd
git config --global init.defaultBranch main
```

---

## 3. Deep-Dive Authentication Strategies

GitHub requires secure authentication before accepting code pushes. You can authenticate using **HTTPS (via Personal Access Tokens)**, **SSH (Asymmetric Keys)**, or **GitHub CLI (OAuth)**.

```
                           +----------------------------+
                           | Authentication Strategy    |
                           +----------------------------+
                               /           |          \
                             /             |            \
                           v               v              v
                  +-------------+   +-------------+   +-------------+
                  |    HTTPS    |   |     SSH     |   | GitHub CLI  |
                  |  (PAT / GCM)|   |  (Ed25519)  |   |   (OAuth)   |
                  +-------------+   +-------------+   +-------------+
```

---

### 3.1 Method A: HTTPS & Personal Access Tokens (PAT)

#### 3.1.1 Why Traditional Passwords Are Deprecated
In August 2021, GitHub permanently discontinued account password authentication for Git operations. Standard passwords are vulnerable to credential stuffing, phishing, and cannot be restricted by repository scopes or expiry dates.

Instead, Git operations over HTTPS require a **Personal Access Token (PAT)**.

#### 3.1.2 Classic Personal Access Tokens (Scope-Based)
Classic tokens apply globally across all repositories accessible by your GitHub account.

1. Navigate to GitHub: **Profile Picture** $\rightarrow$ **Settings**.
2. Scroll to the bottom of the left sidebar and select **Developer settings**.
3. Expand **Personal access tokens** and select **Tokens (classic)**.
4. Click **Generate new token** $\rightarrow$ **Generate new token (classic)**.
5. In the **Note** field, enter an identifier (e.g., `Windows-CMD-Workstation`).
6. Set an **Expiration** date (e.g., `30 days`, `90 days`, or `Custom`).
7. Under **Select scopes**, select:
   - `[x] repo` (Full control of private repositories, commit status, deployment status).
   - `[x] workflow` (Optional: needed if pushing GitHub Actions workflows).
8. Click **Generate token** at the bottom of the page.
9. **Copy the token immediately** (starts with `ghp_...`). It is shown only once.

#### 3.1.3 Fine-Grained Personal Access Tokens (Least-Privilege Security)
Fine-Grained tokens follow the principle of least privilege, restricting access to explicitly chosen repositories.

1. In **Developer settings**, select **Personal access tokens** $\rightarrow$ **Fine-grained tokens**.
2. Click **Generate new token**.
3. Define a **Token name**, **Expiration**, and **Description**.
4. In **Repository access**, select **Only select repositories**, then select your target project.
5. Under **Permissions** $\rightarrow$ **Repository permissions**:
   - Locate **Contents**: Select **Access: Read and write**.
   - Locate **Metadata**: This automatically defaults to **Access: Read-only**.
6. Click **Generate token** and securely save the token string (`github_pat_...`).

#### 3.1.4 Configuring Git Credential Manager (GCM) on Windows
When you run your first `git push` over HTTPS, Windows Git Credential Manager will open a pop-up window:
* Select **Sign in with your browser** to authenticate automatically via OAuth.
* If prompted directly in the terminal for a password:
  - **Username:** Your GitHub username.
  - **Password:** Paste your **Personal Access Token (PAT)** (never type your regular GitHub account password).

---

### 3.2 Method B: Secure Shell (SSH) Cryptographic Integration

SSH enables automated, secure, passwordless authentication without relying on expiring tokens.

#### 3.2.1 Asymmetric Cryptography: Public Key vs. Private Key
SSH relies on mathematically linked asymmetric key pairs:
* **Private Key (`id_ed25519`):** Stored only on your local machine under `%USERPROFILE%\.ssh\`. Never shared, transmitted, or uploaded.
* **Public Key (`id_ed25519.pub`):** Uploaded to GitHub. When authenticating, GitHub sends an encrypted challenge that can only be decrypted by the matching private key on your machine.

#### 3.2.2 Key Algorithms: Ed25519 vs. RSA 4096-bit
* **Ed25519 (Recommended):** Uses elliptic-curve cryptography (Curve25519). Faster, more secure, and produces shorter keys.
* **RSA (Legacy standard):** Uses large integer factorization. Requires at least 4096 bits to match modern security requirements.

#### 3.2.3 Generating the Cryptographic Key Pair
Open Command Prompt or PowerShell and generate an **Ed25519** key:

```cmd
ssh-keygen -t ed25519 -C "your.email@example.com"
```

*(Legacy RSA command: `ssh-keygen -t rsa -b 4096 -C "your.email@example.com"`)*

Interactive prompts:
1. `Enter file in which to save the key`: Press **Enter** to accept the default path (`%USERPROFILE%\.ssh\id_ed25519`).
2. `Enter passphrase (empty for no passphrase)`: Enter a passphrase for local file encryption, or press **Enter** twice for passwordless login.

#### 3.2.4 Configuring the Windows SSH Agent Service
Windows includes an OpenSSH Authentication Agent service to hold your private keys in memory.

Open PowerShell as **Administrator** and run:
```powershell
# Set service startup type to Manual/Automatic
Get-Service -Name ssh-agent | Set-Service -StartupType Manual

# Start the agent service
Start-Service ssh-agent

# Add your private key to the agent
ssh-add $HOME\.ssh\id_ed25519
```

#### 3.2.5 Adding the Public Key to Your GitHub Account
1. Copy the exact contents of your **public** key to your Windows clipboard:
   ```cmd
   clip < %USERPROFILE%\.ssh\id_ed25519.pub
   ```
2. Open GitHub $\rightarrow$ **Settings** $\rightarrow$ **SSH and GPG keys**.
3. Click the green **New SSH key** button.
4. **Title:** Give it a clear name (e.g., `Windows11-Development-Rig`).
5. **Key type:** Keep as `Authentication Key`.
6. **Key:** Paste the clipboard contents (starts with `ssh-ed25519 AAAAC3...`).
7. Click **Add SSH key**.

#### 3.2.6 Testing and Validating the SSH Handshake
Verify your setup by running:
```cmd
ssh -T git@github.com
```

If prompted:
```
The authenticity of host 'github.com (140.82.121.4)' can't be established.
ED25519 key fingerprint is SHA256:+DiY3wvvV6TuJJhbpZisF/zPTO6RxKVzqdcOvNNRFqk.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```
Type **`yes`** and press **Enter**.

A successful connection will return:
> `Hi <username>! You've successfully authenticated, but GitHub does not provide shell access.`

---

### 3.3 Method C: GitHub CLI (`gh`) Web-Based OAuth Flow

The GitHub CLI provides the quickest setup experience by handling tokens and SSH keys automatically through browser-based OAuth.

#### 3.3.1 Installing and Verifying GitHub CLI
Check your installation:
```cmd
gh --version
```

#### 3.3.2 Interactive Authentication via Terminal & Browser
Run the interactive login command:
```cmd
gh auth login
```

Complete the configuration prompts:
1. **What account do you want to log into?** Select `GitHub.com`.
2. **What is your preferred protocol for Git operations?** Select `SSH` (or `HTTPS`).
3. **Generate a new SSH key to upload to your GitHub account?** Select `Yes`.
4. **Enter a passphrase for your SSH key:** (Optional) Press Enter for none.
5. **Title for your SSH key:** Press Enter for default (`GitHub CLI`).
6. **How would you like to authenticate GitHub CLI?** Select `Login with a web browser`.
7. **Copy the one-time code** shown in the terminal (e.g., `ABCD-1234`).
8. Press **Enter** to open the browser window, paste the verification code, and click **Authorize github**.

Verify login state:
```cmd
gh auth status
```

---

## 4. Repository Workflows & Project Bootstrapping

Choose the workflow that matches how you are starting your project:

---

### 4.1 Workflow 1: Clone Remote First (Recommended Standard)

This workflow creates the repository on GitHub first and brings it to your local machine.

```
GitHub (Remote) === git clone ===> Local Machine === Edit/Commit ===> Push to Remote
```

#### Step 1: Create the Remote Repository
1. Go to [github.com/new](https://github.com/new).
2. Set **Repository name** (e.g., `my-awesome-project`).
3. Set visibility to **Public** or **Private**.
4. Check **Add a README file**.
5. Click **Create repository**.

#### Step 2: Clone to Your Local Workstation
Copy the URL from the green **Code** button on GitHub, open CMD, navigate to your work folder, and clone:

* **Using SSH (Recommended):**
  ```cmd
  cd C:\Users\YourUsername\Projects
  git clone git@github.com:username/my-awesome-project.git
  ```
* **Using HTTPS:**
  ```cmd
  cd C:\Users\YourUsername\Projects
  git clone https://github.com/username/my-awesome-project.git
  ```

#### Step 3: Enter Project Directory
```cmd
cd my-awesome-project
```

---

### 4.2 Workflow 2: Initialize Locally First & Link to Remote

This workflow connects an existing local project folder to a brand new, empty GitHub repository.

#### Step 1: Initialize Local Directory
Open CMD inside your project folder:
```cmd
cd C:\Users\YourUsername\Projects\my-local-app
git init
```

#### Step 2: Create Initial Files and First Commit
```cmd
echo # My Local App > README.md
git add README.md
git commit -m "chore: initial repository structure"
```

#### Step 3: Ensure the Branch Name is `main`
```cmd
git branch -M main
```

#### Step 4: Create a Clean Remote Repository on GitHub
Create a new repository on GitHub **without** initializing a README, `.gitignore`, or license.

#### Step 5: Bind the Remote URL
* **For SSH:**
  ```cmd
  git remote add origin git@github.com:username/my-local-app.git
  ```
* **For HTTPS:**
  ```cmd
  git remote add origin https://github.com/username/my-local-app.git
  ```

#### Step 6: Push to Upstream
```cmd
git push -u origin main
```

---

### 4.3 Inspecting and Modifying Remote URLs

Verify existing remote connections:
```cmd
git remote -v
```

Switch an existing HTTPS repository to SSH:
```cmd
git remote set-url origin git@github.com:username/my-local-app.git
```

Switch an existing SSH repository to HTTPS:
```cmd
git remote set-url origin https://github.com/username/my-local-app.git
```

---

## 5. Visual Studio Code Integration

Visual Studio Code provides built-in Git tools alongside an integrated terminal.

```
+-------------------------------------------------------------------------+
|  VISUAL STUDIO CODE                                                     |
|                                                                         |
|  [Explorer]      | [Editor: README.md]          | [Source Control]      |
|  - src/          | # Project Overview           | Staged Changes (+)    |
|  - README.md     |                              |   - README.md         |
|  - .gitignore    |                              | Commit Message Box    |
|                  |                              | [ Commit Button ]     |
|-------------------------------------------------------------------------|
|  [Integrated Terminal: CMD / PowerShell]                                |
|  C:\Projects\my-app> git push origin main                               |
+-------------------------------------------------------------------------+
```

### 5.1 Launching VS Code via Command Line Interface (CLI)
From inside any Git project directory in CMD, open the current folder in VS Code with:
```cmd
code .
```

To open a specific file directly:
```cmd
code README.md
```

---

### 5.2 Navigating the Source Control Panel (GUI)

Press `Ctrl + Shift + G` to open the Source Control panel in the left sidebar:
* **Working Tree Tracking:** Modified, deleted, or untracked files are highlighted in real-time.
* **Staging Area (`+` Icon):** Clicking `+` next to a file runs `git add <file>`. Clicking `+` on the **Changes** header runs `git add .`.
* **Unstaging Changes (`-` Icon):** Clicking `-` next to a staged file runs `git restore --staged <file>`.
* **Inline Diffs:** Clicking any changed file opens a side-by-side diff comparing working changes against the latest commit.
* **Commit Input Box:** Type your message and press `Ctrl + Enter` to execute `git commit -m "message"`.
* **Sync Changes Button:** Synchronizes local commits with the remote branch (`git pull --rebase` followed by `git push`).

---

### 5.3 Integrated Terminal Configuration & Best Practices

Access the built-in terminal using `` Ctrl + ` `` or **Terminal** $\rightarrow$ **New Terminal**.

To set Command Prompt (`cmd.exe`) as your default terminal profile:
1. Press `Ctrl + Shift + P` to open the Command Palette.
2. Type `Terminal: Select Default Profile` and press **Enter**.
3. Select **Command Prompt**.

---

### 5.4 Essential VS Code Extensions for Git & Markdown

Boost productivity with these extensions from the VS Code Marketplace:
* **GitLens (`eamodio.gitlens`):** Visualizes code authorship directly inside each line (`git blame`), provides rich commit graphs, and deep commit exploration.
* **Git Graph (`mhutchie.git-graph`):** Provides an interactive visual commit tree of your branches and tags.
* **Markdown All in One (`yzhang.markdown-all-in-one`):** Adds shortcuts, auto-generated tables of contents, and formatting helpers for Markdown documentation.
* **Markdown Preview Enhanced (`shd101wyy.markdown-preview-enhanced`):** Live side-by-side Markdown preview with math and diagram support.

---

## 6. The Core Git Execution Lifecycle: Add, Commit, Push

```
+------------------+         +---------------------+         +---------------------+
| Working Folder   |         | Staging Area (Index)|         | Local Git (HEAD)    |
| (Unstaged Files) |         | (Tracked Snapshots) |         | (Committed History) |
+------------------+         +---------------------+         +---------------------+
         |                              |                               |
         | -------- git add . --------> |                               |
         |                              | --- git commit -m "msg" ----> |
         |                              |                               |
         | <--- git restore <file> ---- |                               |
         |                              |                               |
```

### 6.1 Staging State Operations (`git add`)

Staging lets you curate which changes belong in the next snapshot.

* **Stage all changes, creations, and deletions:**
  ```cmd
  git add .
  ```
* **Stage a specific file:**
  ```cmd
  git add README.md
  ```
* **Stage an entire directory:**
  ```cmd
  git add docs/
  ```
* **Stage changes interactively (review chunk-by-chunk):**
  ```cmd
  git add -p
  ```
* **Check the staging status:**
  ```cmd
  git status
  ```

---

### 6.2 Writing Deterministic History Snapshots (`git commit`)

A commit saves your staged changes as an immutable checkpoint in the project history.

* **Commit staged changes with a clear message:**
  ```cmd
  git commit -m "docs: write comprehensive guide for github integration"
  ```
* **Conventional Commits standard prefixes:**
  - `feat:` A new feature or capability.
  - `fix:` A bug fix.
  - `docs:` Documentation-only changes.
  - `refactor:` Code restructuring that does not fix a bug or add a feature.
  - `chore:` Maintenance tasks, dependency updates, build tooling.
* **Amending the most recent commit (fix typos before pushing):**
  ```cmd
  git commit --amend -m "docs: revised commit message here"
  ```

---

### 6.3 Remote Upstream Synchronization (`git push`)

Pushing uploads your local commits to the remote GitHub repository.

* **First push (establishes upstream tracking reference):**
  ```cmd
  git push -u origin main
  ```
  *The `-u` (or `--set-upstream`) flag links your local `main` branch to `origin/main`.*

* **Subsequent pushes (once upstream is linked):**
  ```cmd
  git push
  ```

---

### 6.4 Fetching and Pulling Remote Upgrades (`git pull` & Rebase)

When collaborating, you need to pull remote changes into your local workspace.

* **Fetch remote tracking branches without modifying local files:**
  ```cmd
  git fetch origin
  ```
* **Pull and merge remote updates:**
  ```cmd
  git pull origin main
  ```
* **Pull with rebase (creates a clean, linear commit history):**
  ```cmd
  git pull --rebase origin main
  ```

---

## 7. Comprehensive Troubleshooting & Diagnostics

### 7.1 Warning: `LF will be replaced by CRLF`
* **Symptom:**
  ```
  warning: in the working copy of 'README.md', LF will be replaced by CRLF the next time Git touches it
  ```
* **Explanation:** Git is notifying you that Unix line-breaks (`LF`) will be converted to Windows line-breaks (`CRLF`) during checkout.
* **Fix:** Enforce automated line-ending conversion:
  ```cmd
  git config --global core.autocrlf true
  ```

---

### 7.2 Error: `Support for password authentication was removed`
* **Symptom:**
  ```
  remote: Support for password authentication was removed on August 13, 2021.
  fatal: Authentication failed for 'https://github.com/user/repo.git/'
  ```
* **Fix:** You entered your account password instead of a token.
  1. Generate a **Personal Access Token** under GitHub **Developer Settings**.
  2. Clear the cached Windows credentials:
     ```cmd
     cmdkey /delete:LegacyGeneric:target=git:https://github.com
     ```
  3. Re-run `git push` and paste your PAT when prompted for the password.

---

### 7.3 Error: `Permission denied (publickey)`
* **Symptom:**
  ```
  git@github.com: Permission denied (publickey).
  fatal: Could not read from remote repository.
  ```
* **Fix:** Your SSH key is missing, inactive, or not registered on GitHub.
  1. Verify the public key is added under GitHub **Settings $\rightarrow$ SSH and GPG keys**.
  2. Confirm your key is loaded into the SSH agent:
     ```cmd
     ssh-add %USERPROFILE%\.ssh\id_ed25519
     ```
  3. Test the connection:
     ```cmd
     ssh -T git@github.com
     ```

---

### 7.4 Error: `fatal: remote origin already exists`
* **Symptom:**
  ```
  fatal: remote origin already exists.
  ```
* **Fix:** A remote named `origin` is already assigned to this repository.
  * Check current remotes:
    ```cmd
    git remote -v
    ```
  * Update the URL directly:
    ```cmd
    git remote set-url origin git@github.com:username/repository.git
    ```

---

### 7.5 Error: `Updates were rejected because the remote contains work that you do not have`
* **Symptom:**
  ```
  ! [rejected]        main -> main (fetch first)
  error: failed to push some refs to 'github.com:user/repo.git'
  hint: Updates were rejected because the remote contains work that you do not have locally.
  ```
* **Explanation:** The remote repository contains commits that do not exist locally (e.g., a README or license initialized on GitHub).
* **Fix:** Pull remote commits with rebase before pushing:
  ```cmd
  git pull --rebase origin main
  git push origin main
  ```

---

## 8. Quick Reference Cheat Sheet

| Task | Command |
| :--- | :--- |
| **Check installed versions** | `git --version` / `code --version` / `gh --version` |
| **Configure username** | `git config --global user.name "Your Name"` |
| **Configure user email** | `git config --global user.email "your.email@example.com"` |
| **Initialize new repository** | `git init` |
| **Clone remote repository** | `git clone <remote-url>` |
| **Inspect working tree state** | `git status` |
| **Stage all changes** | `git add .` |
| **Commit staged changes** | `git commit -m "descriptive message"` |
| **Create/Rename default branch**| `git branch -M main` |
| **Bind remote URL** | `git remote add origin <remote-url>` |
| **Change remote URL** | `git remote set-url origin <new-url>` |
| **Push and set upstream** | `git push -u origin main` |
| **Push future changes** | `git push` |
| **Pull latest changes** | `git pull --rebase origin main` |
| **Launch VS Code in folder** | `code .` |
| **Generate Ed25519 SSH Key** | `ssh-keygen -t ed25519 -C "email@example.com"` |
| **Copy SSH Public Key** | `clip < %USERPROFILE%\.ssh\id_ed25519.pub` |
| **Test GitHub SSH connection**| `ssh -T git@github.com` |
| **GitHub CLI Quick Login** | `gh auth login` |