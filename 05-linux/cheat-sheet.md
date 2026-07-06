# Linux Cheatsheet

## Navigation

| Command | Description |
|---------|-------------|
| `pwd` | Print the current working directory. |
| `ls` | List files and directories. |
| `ls -la` | Show all files, including hidden ones, with detailed information. |
| `cd folder` | Change to another directory. |
| `cd ..` | Go to the parent directory. |
| `cd ~` | Go to your home directory. |

---

## File Operations

| Command | Description |
|---------|-------------|
| `touch file.txt` | Create an empty file. |
| `mkdir folder` | Create a new directory. |
| `mkdir -p a/b/c` | Create nested directories if they don't exist. |
| `cp file backup.txt` | Copy a file. |
| `cp -r src dst` | Copy an entire directory recursively. |
| `mv old new` | Move or rename a file or folder. |
| `rm file.txt` | Delete a file. |
| `rm -rf folder` | Force delete a directory and everything inside it. **⚠️ Use carefully.** |

---

## Search

| Command | Description |
|---------|-------------|
| `find . -name "*.py"` | Find all Python files from the current directory. |
| `grep -rn "TODO" .` | Search recursively for text inside files. |
| `which python` | Show the executable path of a command. |

...