# Getting Started with MXSH

This guide walks you through installing, configuring, and using MXSH for the first time.

## Installation

### Building from Source

MXSH requires the Rust toolchain. If you don't have Rust installed, get it from [rustup.rs](https://rustup.rs).

Clone the repository and build:

```bash
git clone https://github.com/Mxshellit/Mxsh.git
cd Mxsh
cargo build --release
```

The compiled binary will be located at `target/release/mxsh`.

### Adding to PATH

To use MXSH from anywhere, add the binary to your system PATH:

**Linux/macOS:**
```bash
sudo cp target/release/mxsh /usr/local/bin/
```

**Windows:**
Copy `mxsh.exe` to a directory in your PATH or add the `target/release` directory to your system PATH environment variable.

## Initial Configuration

### Setting Up Your API Key

MXSH uses OpenAI's API for command analysis and translation. You'll need an API key from [platform.openai.com](https://platform.openai.com).

Run the initialization command:

```bash
mxsh init
```

This command:
- Creates the `.mxsh` directory in your home folder
- Prompts for your OpenAI API key
- Encrypts and stores the key securely
- Sets default configuration options

The API key is encrypted using machine-specific parameters, ensuring it remains secure even if the configuration files are accessed.

### Selecting Your Preferred Model

MXSH supports multiple OpenAI models with different performance characteristics:

```bash
mxsh models
```

Available models:
- **GPT-5.2 Pro**: Highest accuracy, best for complex command analysis
- **GPT-5.2**: Balanced performance and speed
- **GPT-5**: Standard model for most use cases
- **GPT-5 Mini**: Faster responses, reduced cost
- **GPT-5 Nano**: Minimal cost, basic analysis only
- **GPT-4.1**: Previous generation model

**Note**: Lightweight models (Mini, Nano) disable the Auto Resizer feature to reduce API calls.

### Choosing Your Default Shell

Configure which shell MXSH uses for command execution:

```bash
mxsh shells
```

This setting determines where commands are routed when you don't use a shell-specific prefix. The default shell is automatically set based on your operating system:
- Windows: PowerShell
- macOS: Zsh
- Linux: Bash

## Basic Usage

### Running Your First Command

Simply launch MXSH and enter a command:

```bash
mxsh
```

Try a safe command to verify everything works:

```bash
whoami
```

### Understanding Command Flow

When you enter a command, MXSH performs several checks:

1. **Whitelisting**: Safe read-only commands execute immediately
2. **Auto Resizer**: Cross-platform commands are converted if needed
3. **AI Analysis**: Security assessment determines risk level
4. **User Confirmation**: Medium/low risk commands request approval
5. **Execution**: The command runs in the appropriate shell

### Shell Prefixes

Force execution in a specific shell using prefixes:

```bash
$: Get-Process        # PowerShell
#: ps aux              # Bash
>: dir /w             # CMD
```

### Interactive vs Command Mode

Run MXSH interactively:
```bash
mxsh
```

Execute a single command:
```bash
mxsh "ls -la"
```

## Common First-Time Tasks

### Checking System Information

View your current user and operating system:

```bash
whoami
```

### Monitoring System Resources

Display the top 10 processes by CPU usage:

```bash
cpu
```

### Changing Directories

MXSH includes an enhanced `cd` command:

```bash
cd ~/projects          # Navigate to home directory
cd -                   # Return to previous directory
cd ..                  # Move up one level
```

### Getting Help

Display the complete command reference:

```bash
help
```

## Understanding Security Prompts

When MXSH analyzes a command, you may see output like:

```
Analyzing command...

Risk Level: MEDIUM
Summary: This command modifies system files
Details: The operation requires elevated privileges and changes persistent configuration

Do you want to execute this command anyway? (y/n):
```

Risk levels:
- **SAFE**: Executes automatically
- **LOW/MEDIUM**: Requires user confirmation
- **HIGH/CRITICAL**: Blocked by default

### Bypassing Checks

For trusted commands, skip AI analysis:

```bash
rm -rf temp/ --skip-ai-check
```

For download commands, skip DNS validation:

```bash
curl http://example.com/script.sh --skip-dns-check
```

**Warning**: Bypass flags disable security features. Use only when you trust the command source.

## Environment Variables

### Saving Encrypted Variables

Store sensitive information securely:

```bash
env save API_TOKEN
```

MXSH will prompt for the value and encrypt it.

### Listing Stored Variables

View all saved environment variables:

```bash
env
```

### Removing Variables

Delete a stored variable:

```bash
env remove API_TOKEN
```

### Importing and Exporting

Export variables to an encrypted file:

```bash
env export *                    # Export all variables
env export DATABASE_URL         # Export specific variable
```

Import from an encrypted file:

```bash
env import variables.mxenv
```

## Next Steps

Now that you have MXSH configured:

1. Explore the [Features](features.md) documentation for detailed capability descriptions
2. Review the [Security](security.md) guide to understand protection mechanisms
3. Check the [Commands](commands.md) reference for all available built-in commands
4. Customize behavior through [Configuration](configuration.md) options

## Troubleshooting

### API Key Issues

If commands fail with authentication errors:

```bash
mxsh init
```

Re-enter your API key to reset the configuration.

### Command Not Found

Ensure MXSH is in your PATH:

```bash
which mxsh    # Linux/macOS
where mxsh    # Windows
```

### Unexpected Command Conversions

Disable Auto Resizer by selecting a lightweight model:

```bash
mxsh models
# Select "GPT-5 Nano" or "GPT-5 Mini"
```
