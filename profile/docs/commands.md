# Command Reference

Complete reference for all MXSH built-in commands.

## Built-In Commands

### help

Display comprehensive help information including all available commands, shell prefixes, and environment variable operations.

**Usage:**
```bash
help
h
?
/?
```

**Output:**
- List of all built-in commands with descriptions
- Shell prefix syntax
- Environment variable command reference
- Link to documentation

**Example:**
```bash
help
```

---

### init

Initialize MXSH by creating the configuration directory structure and setting up your OpenAI API key.

**Usage:**
```bash
init
```

**Behavior:**
1. Creates `.mxsh` directory in your home folder
2. Prompts for OpenAI API key
3. Encrypts and stores the API key
4. Sets up default configuration

**First-Time Setup:**
```bash
mxsh init
```

You'll be prompted:
```
Enter your OpenAI API key:
```

**Notes:**
- Run this command before using AI-powered features
- The API key is encrypted using machine-specific data
- Rerun `init` to update your API key

---

### whoami

Display information about the current user and operating system.

**Usage:**
```bash
whoami
```

**Output:**
- Current username
- Operating system name and version

**Example:**
```bash
whoami
```

Output:
```
User: john
OS: Linux
```

---

### models

Interactive model selection interface for choosing which OpenAI model MXSH uses for command analysis.

**Usage:**
```bash
models
```

**Available Models:**
- GPT-5.2 Pro
- GPT-5.2
- GPT-5
- GPT-5 Mini (Auto Resizer disabled)
- GPT-5 Nano (Auto Resizer disabled)
- GPT-4.1

**Selection Process:**
1. Displays current model
2. Lists available options
3. Prompts for selection
4. Saves choice to configuration

**Example:**
```bash
models
```

Output:
```
Current model: GPT-5

Available models:
1. GPT-5.2 Pro
2. GPT-5.2
3. GPT-5
4. GPT-5 Mini
5. GPT-5 Nano
6. GPT-4.1

Select a model (1-6):
```

---

### shells

Configure the default shell used for command execution.

**Usage:**
```bash
shells
```

**Behavior:**
1. Displays current default shell
2. Lists available shells for your platform
3. Prompts for selection
4. Saves configuration

**Platform-Specific Options:**

**Windows:**
- PowerShell
- CMD
- Bash (if WSL installed)

**Linux:**
- Bash
- Zsh
- Fish
- Sh

**macOS:**
- Zsh
- Bash

**Example:**
```bash
shells
```

Output:
```
Current shell: Bash

Available shells:
1. Bash
2. Zsh
3. PowerShell

Select a shell (1-3):
```

---

### cpu

Display the top 10 processes sorted by CPU usage.

**Usage:**
```bash
cpu
```

**Output:**
- Process name
- CPU usage percentage
- Memory usage
- Process ID (PID)

**Example:**
```bash
cpu
```

Output:
```
Top 10 processes by CPU usage:

1. chrome (23456) - CPU: 45.2% - Mem: 1.2 GB
2. node (34567) - CPU: 23.1% - Mem: 512 MB
3. code (45678) - CPU: 12.8% - Mem: 890 MB
...
```

**Notes:**
- Information refreshes based on current system state
- Useful for identifying resource-intensive processes

---

### cd

Change the current working directory with enhanced features.

**Usage:**
```bash
cd [path]
cd          # Navigate to home directory
cd -        # Return to previous directory
```

**Supported Path Formats:**
- Absolute paths: `/usr/local/bin`
- Relative paths: `../other-folder`
- Tilde expansion: `~/projects`
- Previous directory: `-`

**Examples:**

Navigate to home:
```bash
cd
cd ~
```

Relative navigation:
```bash
cd ../parent-folder
cd ./subfolder
```

Previous directory:
```bash
cd /tmp
cd ~/projects
cd -          # Returns to /tmp
```

**Notes:**
- The `-` option maintains a history of the last directory
- Tilde expansion works on all platforms
- Invalid paths display an error message

---

### env

Manage encrypted environment variables.

**Usage:**
```bash
env                         # List all variables
env save <name>            # Save a new variable
env remove <name>          # Remove a variable
env export <name|*>        # Export to encrypted file
env import <file>          # Import from encrypted file
```

### env (no arguments)

List all saved environment variable names.

**Example:**
```bash
env
```

Output:
```
Stored environment variables:
- DATABASE_URL
- API_TOKEN
- SECRET_KEY
```

### env save

Save a new encrypted environment variable.

**Usage:**
```bash
env save VARIABLE_NAME
```

**Behavior:**
1. Prompts for the variable value
2. Encrypts the value
3. Stores in MXSH configuration

**Example:**
```bash
env save DATABASE_URL
```

Prompt:
```
Enter value for DATABASE_URL:
[input hidden]

Variable saved successfully.
```

### env remove

Delete a stored environment variable.

**Usage:**
```bash
env remove VARIABLE_NAME
```

**Example:**
```bash
env remove OLD_TOKEN
```

Output:
```
Variable OLD_TOKEN removed successfully.
```

### env export

Export encrypted variables to a `.mxenv` file.

**Usage:**
```bash
env export VARIABLE_NAME     # Export single variable
env export *                  # Export all variables
```

**Example:**
```bash
env export DATABASE_URL
```

Output:
```
Variable DATABASE_URL exported to DATABASE_URL.mxenv
```

Export all:
```bash
env export *
```

Output:
```
All variables exported to environment.mxenv
```

**Notes:**
- Export files use the same encryption as internal storage
- Files can be imported on other machines with the correct password
- Useful for backup or sharing configurations

### env import

Import encrypted variables from a `.mxenv` file.

**Usage:**
```bash
env import <filename>
```

**Example:**
```bash
env import backup.mxenv
```

Prompt:
```
Enter password for encrypted file:
[input hidden]

3 variables imported successfully.
```

**Notes:**
- Password must match the one used during export
- Existing variables with the same name are overwritten
- Import validation ensures file integrity

---

### credit / credits

Display project contributors and acknowledgments.

**Usage:**
```bash
credit
credits
```

**Output:**
- Project contributors
- Acknowledgments
- License information

**Example:**
```bash
credit
```

---

### clear / new

Clear the terminal screen and redisplay the MXSH message of the day (MOTD).

**Usage:**
```bash
clear
new
```

**Behavior:**
1. Clears the terminal display
2. Shows MXSH banner
3. Displays system information
4. Resets to a clean prompt

**Example:**
```bash
clear
```

---

### exit / quit / q / :q

Exit the MXSH shell and return to the parent shell.

**Usage:**
```bash
exit
quit
q
:q
```

**Example:**
```bash
exit
```

Output:
```
Goodbye!
```

**Notes:**
- All variants perform the same function
- `:q` provided for Vim users
- Unsaved work in the current shell session will be lost

---

## Shell Prefix Commands

Execute commands in specific shells regardless of your default configuration.

### PowerShell Prefix: `$:`

Execute command in PowerShell.

**Usage:**
```bash
$: <powershell-command>
```

**Example:**
```bash
$: Get-Process
$: Get-ChildItem -Recurse
```

---

### Bash Prefix: `#:`

Execute command in Bash.

**Usage:**
```bash
#: <bash-command>
```

**Example:**
```bash
#: ls -la
#: grep -r "pattern" .
```

---

### CMD Prefix: `>:`

Execute command in Windows Command Prompt.

**Usage:**
```bash
>: <cmd-command>
```

**Example:**
```bash
>: dir /w
>: ipconfig /all
```

---

## Command Flags

### --skip-ai-check

Bypass AI-powered security analysis for the command.

**Usage:**
```bash
<command> --skip-ai-check
```

**Example:**
```bash
rm -rf temp/ --skip-ai-check
```

**Warning:**
- Disables primary security mechanism
- Command executes without risk assessment
- Use only for trusted operations
- You will be prompted for confirmation

---

### --skip-dns-check

Bypass DNS-based security validation for download commands.

**Usage:**
```bash
<curl|wget-command> --skip-dns-check
```

**Example:**
```bash
curl http://internal-server/script.sh --skip-dns-check
```

**Warning:**
- Allows downloads from potentially malicious sources
- Bypasses domain reputation checking
- Use only for trusted private or internal resources
- You will be prompted for confirmation

---

## Whitelisted Commands

The following commands execute immediately without AI analysis when used with only option flags:

**Directory Operations:**
- `ls`, `dir`: List directory contents

**System Information:**
- `pwd`: Print working directory
- `hostname`: System hostname
- `whoami`: Current user
- `date`, `time`: Date and time information

**File Reading:**
- `cat`: Display file contents
- `head`: Display file beginning
- `tail`: Display file end
- `type`: Display file (Windows)

**Utilities:**
- `echo`: Print text
- `wc`: Word count
- `tree`: Directory structure

**Version Control:**
- `git`: All git commands (fully whitelisted)

**Notes:**
- Whitelisted commands with file/directory arguments undergo security analysis
- Only option flags (starting with `-`) bypass analysis
- Presence of dangerous patterns (pipes, redirects) triggers analysis
