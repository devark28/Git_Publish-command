# Git Publish Command

This guide will walk you through setting up a custom `git publish` command that creates a GitHub repository from your local project and pushes it in one step.

## Prerequisites

- Git installed on your system
- A GitHub account
- Basic familiarity with command line operations

## Installation Steps

1. **Install GitHub CLI (gh)**

   For Ubuntu 20.04:
   ```bash
   curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg
   echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null
   sudo apt update
   sudo apt install gh
   ```

   For other operating systems, visit: https://github.com/cli/cli#installation

2. **Authenticate GitHub CLI**

   Run the following command and follow the prompts:
   ```bash
   gh auth login
   ```

3. **Create the git-publish script**

   Create a new file named `git-publish` in a directory that's in your PATH. For example:
   ```bash
   mkdir -p ~/.local/bin
   nano ~/.local/bin/git-publish
   ```

4. **Add the script content**

   Copy and paste the following content into the `git-publish` file:

   ```bash
   #!/bin/bash

   # Redirect all output to /dev/null by default
   exec 3>&1
   exec 1>/dev/null 2>&1

   # Function to clean up and exit
   cleanup_and_exit() {
       exec 1>&3
       exit $1
   }

   # Check if current directory is a git repository
   if ! git rev-parse --is-inside-work-tree > /dev/null 2>&1; then
       git init || cleanup_and_exit 1
   fi

   # Get the current directory name as the repo name
   repo_name=${PWD##*/}

   # Add all files and commit if there are changes
   if [[ -n $(git status -s) ]]; then
       git add . || cleanup_and_exit 1
       git commit -m "Initial commit" || cleanup_and_exit 1
   fi

   # Create a new repository on GitHub and push
   if ! gh repo create "$repo_name" --public --source=. --remote=origin; then
       cleanup_and_exit 1
   fi

   # Push to the new repository
   if ! git push -u origin HEAD; then
       cleanup_and_exit 1
   fi

   # If we've made it this far, everything succeeded
   exec 1>&3
   echo "Repository created and pushed successfully!"
   exit 0
   ```
   <b>Note:</b> you can use the content of [this file](https://github.com/devark28/Git_Publish-command/blob/main/git-publish) for simplicity

6. **Make the script executable**

   Run the following command:
   ```bash
   chmod +x ~/.local/bin/git-publish
   ```

7. **Add the script's directory to your PATH**

   If `~/.local/bin` is not already in your PATH, add it by appending the following line to your shell configuration file (`~/.bashrc`, `~/.bash_profile`, or `~/.zshrc`):

   ```bash
   export PATH="$HOME/.local/bin:$PATH"
   ```

   Then, reload your shell configuration:
   ```bash
   source ~/.bashrc  # or ~/.bash_profile or ~/.zshrc
   ```

## Usage

To use the `git publish` command:

1. Navigate to your project directory
2. Run the command:
   ```bash
   git publish
   ```

The command will initialize a git repository (if needed), create a new GitHub repository with the same name as your current directory, and push your local files to it.

## Troubleshooting

- If you encounter permission issues, ensure the script is executable and located in a directory in your PATH.
- If you see GitHub authentication errors, run `gh auth login` again to reauthenticate.
- For any other issues, check that GitHub CLI (gh) is properly installed and configured.

## Contributing

Feel free to fork this project and submit pull requests with any improvements or bug fixes.

## License

This project is open source and available under the [MIT License](LICENSE).
