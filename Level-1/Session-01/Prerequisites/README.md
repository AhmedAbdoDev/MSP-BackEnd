# Session 1 Prerequisites

## Prepare Your Development Environment

Before attending Session 1, make sure the following applications are installed and working on your computer.

The goal is to spend the session learning and practicing, not troubleshooting installations.

## 1. Node.js 26

We will use **Node.js 26** during the training.

Node.js is the JavaScript runtime that allows us to execute JavaScript code outside the browser. It also includes npm, which we will use later to install project dependencies and packages.

### Download and install

1. Open the official Node.js website: https://nodejs.org/en/download
2. Select the **26.x** version.
3. Choose your operating system and download the appropriate installer.
4. Run the installer and follow the installation steps.
5. Keep the default installation options unless you have a specific reason to change them.

### Verify the installation

Open a terminal (Command Prompt, PowerShell, or the VS Code terminal) and run:

```bash
node -v
npm -v
```

The first command should print a Node.js version beginning with `v26`. The second should print the installed npm version.

If the commands are not recognized, close and reopen your terminal. If the issue remains, restart your computer and try again.

## 2. Visual Studio Code

We will use **Visual Studio Code (VS Code)** as our code editor.

Make sure you download Visual Studio Code, not Visual Studio.

### Download and install

1. Open the official website: https://code.visualstudio.com/
2. Download the version for your operating system.
3. Run the installer and complete the installation.
4. Open VS Code to make sure it launches correctly.

### Verify the terminal

1. Open VS Code.
2. From the menu, select **Terminal → New Terminal**.
3. Run:

```bash
node -v
npm -v
```

Make sure the same version information appears in the VS Code terminal.

## 3. Postman

We will use **Postman** to send requests to backend endpoints and inspect the responses.

### Download and install

1. Open the official website: https://www.postman.com/downloads/
2. Download Postman for your operating system.
3. Install it and open the application.
4. Complete any initial setup steps shown by the application.

You do not need to create API requests before the session. Just make sure Postman is installed and opens successfully.

## Final Checklist

Before attending Session 1, confirm that:

- [ ] Node.js 26 is installed.
- [ ] `node -v` prints a version beginning with `v26`.
- [ ] `npm -v` prints a version number.
- [ ] Visual Studio Code is installed and opens.
- [ ] The VS Code integrated terminal works.
- [ ] Postman is installed and opens.

## Need Help?

If you cannot install any of the required tools, do not wait until the session starts.

Contact the training team before the session and send:
- Your operating system.
- The step where the problem happened.
- A screenshot of the error message, if available.
