## Create the directory
- `mkdir -p ~/.local/bin`
## Create file
- `touch ~/.local/bin/git-publish`
## Edit the file in your cli editor (nano, vim, etc.)
- `vim ~/.local/bin/git-publish`
## Write the content of git-publish to this file
- `[Use the content of this file](https://github.com/devark28/Git_Publish-command/blob/main/git-publish)`
## Make script executable
- `chmod +x ~/.local/bin/git-publish`
## add directory to path
- `export PATH="$HOME/.local/bin:$PATH"`
## Verify
- `which publish`
