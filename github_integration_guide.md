# GitHub & VS Code Integration Manual

A complete, step-by-step guide to configure Git, link your personal GitHub repository using CMD via **HTTPS**, **SSH**, or **GitHub CLI**, open your project in VS Code, and successfully push your code.

---

## Prerequisites

Make sure you have the following installed on your system:
1. **Git for Windows** (Download from [git-scm.com](https://git-scm.com/))
2. **VS Code** (Download from [code.visualstudio.com](https://code.visualstudio.com/))

Open your **Command Prompt (CMD)** and verify that Git is installed correctly:
```cmd
git --version
```

---

## Step 1: Configure Git with Your Details

Before linking your repository, set up your global Git username and email (use the email associated with your GitHub account):

```cmd
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

---

## Step 2: Choose Your Authentication Method

Choose **one** of the three methods below to authenticate and link your local project to GitHub:

### Option A: Using HTTPS (Recommended for Beginners)
1. Go to GitHub, create a new repository (e.g., `my-project`), and copy its **HTTPS URL** (e.g., `https://github.com/username/my-project.git`).
2. Open your project folder in CMD or navigate to it:
   ```cmd
   cd path\to\your\project
   ```
3. Initialize Git and link the remote repository:
   ```cmd
   git init
   git remote add origin https://github.com/username/my-project.git
   ```
   > **Note:** GitHub no longer accepts account passwords for HTTPS authentication. You must use a **Personal Access Token (PAT)**. When Git prompts you for a password during a push/pull, paste your PAT instead of your GitHub password.

---

### Option B: Using SSH (Secure and Passwordless)
1. Check if you already have an existing SSH key:
   ```cmd
   dir %userprofile%\.ssh
   ```
2. If no key exists, generate a new SSH key:
   ```cmd
   ssh-keygen -t rsa -b 4096 -C "your.email@example.com"
   ```
   *(Press Enter for all prompt questions to keep default settings).*
3. Start the SSH agent and add your key:
   ```cmd
   ssh-agent
   ssh-add %userprofile%\.ssh\id_rsa
   ```
4. Copy your public SSH key to the clipboard:
   ```cmd
   clip < %userprofile%\.ssh\id_rsa.pub
   ```
5. Go to GitHub -> **Settings** -> **SSH and GPG keys** -> Click **New SSH key**, paste your copied key, and save.
6. Link your local project to the repository using the **SSH URL**:
   ```cmd
   cd path\to\your\project
   git init
   git remote add origin git@github.com:username/my-project.git
   ```

---

### Option C: Using GitHub CLI (`gh`)
1. Ensure GitHub CLI is installed (`gh --version`). If not, download and install it from [cli.github.com](https://cli.github.com/).
2. Log in to your GitHub account via CMD:
   ```cmd
   gh auth login
   ```
3. Follow the interactive prompts:
   - Select **GitHub.com**
   - Choose your preferred protocol (**HTTPS** or **SSH**)
   - Authenticate via **Login with a web browser** (copy the one-time code and paste it into the browser window that opens).
4. Once authenticated, you can easily clone or link your repository directly.

---

## Step 3: Open and Work in VS Code

Once your project folder is initialized and linked via Git, you can open it directly in VS Code using CMD:

```cmd
code .
```

Alternatively, open VS Code manually and click **File > Open Folder** to select your project directory.

---

## Step 4: Push Your Code to GitHub

After making changes or adding files to your project in VS Code (or via CMD terminal), follow these steps to push them to GitHub:

1. **Stage your changes** (add all modified or new files):
   ```cmd
   git add .
   ```

2. **Commit your changes** with a descriptive message:
   ```cmd
   git commit -m "Initial commit"
   ```

3. **Push your code** to the main branch on GitHub:
   ```cmd
   git branch -M main
   git push -u origin main