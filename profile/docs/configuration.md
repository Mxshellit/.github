# Configuration Guide

MXSH stores configuration in the `.mxsh` directory located in your home folder. This guide explains the configuration structure, options, and customization possibilities.

## Configuration Directory Structure

```
~/.mxsh/
├── credentials/
│   └── openai_api_key.enc        # Encrypted API key
├── models/
│   └── data.json                  # Selected AI model
├── config/
│   └── shells.json                # Default shell configuration
└── env/
    ├── store.json                 # Encrypted environment variables
    └── exports/                   # Exported .mxenv files
```

## File Locations

**Unix-like Systems (Linux, macOS):**
```
/home/username/.mxsh/
/Users/username/.mxsh/
```

**Windows:**
```
C:\Users\Username\.mxsh\
```

## Configuration Files

### OpenAI API Key

**Location:** `~/.mxsh/credentials/openai_api_key.enc`

**Format:** Encrypted binary data (not human-readable)

**Management:**

Set or update:
```bash
mxsh init
```

**Security:**
- Encrypted using AES-256-GCM
- Key derived from machine-specific parameters
- Cannot be decrypted on different hardware

**Manual Removal:**

If you need to remove the stored API key:
```bash
rm ~/.mxsh/credentials/openai_api_key.enc
```

Next command requiring API access will fail. Run `mxsh init` to configure a new key.

### Model Configuration

**Location:** `~/.mxsh/models/data.json`

**Format:** JSON

**Structure:**
```json
{
  "selected_model": "GPT-5"
}
```

**Valid Values:**
- `"GPT-5.2 Pro"`
- `"GPT-5.2"`
- `"GPT-5"`
- `"GPT-5 Mini"`
- `"GPT-5 Nano"`
- `"GPT-4.1"`

**Management:**

Interactive selection:
```bash
mxsh models
```

**Manual Editing:**

Edit the file directly:
```bash
nano ~/.mxsh/models/data.json
```

Change the `selected_model` value to your preferred model.

**Default:**
If this file doesn't exist, MXSH uses `GPT-5 Nano` as the default model.

### Shell Configuration

**Location:** `~/.mxsh/config/shells.json`

**Format:** JSON

**Structure:**
```json
{
  "selected_shell": "PowerShell",
  "shell_command": "powershell"
}
```

**Fields:**

- `selected_shell`: Display name of the shell
- `shell_command`: Executable name used to invoke the shell

**Common Configurations:**

**PowerShell:**
```json
{
  "selected_shell": "PowerShell",
  "shell_command": "powershell"
}
```

**Bash:**
```json
{
  "selected_shell": "Bash",
  "shell_command": "bash"
}
```

**Zsh:**
```json
{
  "selected_shell": "Zsh",
  "shell_command": "zsh"
}
```

**CMD:**
```json
{
  "selected_shell": "CMD",
  "shell_command": "cmd"
}
```

**Management:**

Interactive selection:
```bash
mxsh shells
```

**Manual Editing:**
```bash
nano ~/.mxsh/config/shells.json
```

**Platform Defaults:**

If no configuration exists, MXSH uses:
- **Windows**: PowerShell
- **macOS**: Zsh
- **Linux**: Bash

### Environment Variable Storage

**Location:** `~/.mxsh/env/store.json`

**Format:** JSON with encrypted values

**Structure:**
```json
{
  "variables": {
    "DATABASE_URL": {
      "encrypted_value": "base64-encoded-encrypted-data",
      "nonce": "base64-encoded-nonce",
      "salt": "base64-encoded-salt"
    }
  }
}
```

**Management:**

Use the `env` command - do not manually edit this file.

```bash
env save VARIABLE_NAME
env remove VARIABLE_NAME
env
```

**Security:**
- All values encrypted with AES-256-GCM
- Each value has unique nonce
- Salt used for key derivation

**Backup:**

Export encrypted variables:
```bash
env export *
```

Creates `environment.mxenv` file in the current directory.

## Environment Variable Exports

**Location:** User-specified (typically current directory)

**Format:** `.mxenv` files (encrypted JSON)

**Structure:**
```json
{
  "version": 1,
  "encrypted_data": "base64-encoded-encrypted-payload",
  "nonce": "base64-encoded-nonce",
  "salt": "base64-encoded-salt"
}
```

**Management:**

Export:
```bash
env export VARIABLE_NAME          # Creates VARIABLE_NAME.mxenv
env export *                       # Creates environment.mxenv
```

Import:
```bash
env import filename.mxenv
```

**Security:**
- Password-based encryption (user provides password)
- Can be safely transmitted or stored externally
- Requires password for decryption

## Advanced Configuration

### Custom Shell Integration

To use a custom shell not listed in the default options:

1. Edit `~/.mxsh/config/shells.json`
2. Set `shell_command` to your shell's executable
3. Set `selected_shell` to a descriptive name

Example for Fish shell:
```json
{
  "selected_shell": "Fish",
  "shell_command": "fish"
}
```

**Requirements:**
- Shell must be installed and in PATH
- Shell must support `-c` flag for command execution

### Multiple API Keys (Advanced)

MXSH supports only one API key at a time in the standard configuration. For advanced use cases requiring multiple keys:

**Workaround:**
1. Use separate user accounts or containers
2. Each instance maintains its own `.mxsh` directory
3. Run `mxsh init` in each environment with different keys

### Environment Variable Encryption Keys

**Machine-Based Variables:**

Encryption key derived from:
- Hardware identifiers
- System UUID
- Platform-specific parameters

**Password-Based Variables:**

Encryption key derived from:
- User-provided password
- Random salt (stored with encrypted data)
- Argon2 key derivation function

## Configuration Backup

### Full Backup

Backup entire configuration:

```bash
tar -czf mxsh-backup.tar.gz ~/.mxsh/
```

**Includes:**
- API key (encrypted, machine-specific)
- Model selection
- Shell preferences
- Environment variables (encrypted)

### Restore Backup

```bash
tar -xzf mxsh-backup.tar.gz -C ~/
```

**Notes:**
- API key may not work on different hardware (machine-based encryption)
- Environment variables restore successfully
- Model and shell settings transfer without issues

### Portable Backup

For backup that works on different machines:

```bash
# Export environment variables
env export *

# Copy model configuration
cp ~/.mxsh/models/data.json model-config.json

# Copy shell configuration
cp ~/.mxsh/config/shells.json shell-config.json

# Store API key separately (you'll need to re-enter it)
```

Restore on new system:
```bash
# Initialize MXSH with API key
mxsh init

# Restore configurations
mkdir -p ~/.mxsh/models ~/.mxsh/config
cp model-config.json ~/.mxsh/models/data.json
cp shell-config.json ~/.mxsh/config/shells.json

# Import environment variables
env import environment.mxenv
```

## Resetting Configuration

### Complete Reset

Remove all MXSH configuration:

```bash
rm -rf ~/.mxsh/
```

Next run of MXSH will prompt for initialization:
```bash
mxsh init
```

### Partial Resets

**Reset API Key Only:**
```bash
rm ~/.mxsh/credentials/openai_api_key.enc
mxsh init
```

**Reset Model Selection:**
```bash
rm ~/.mxsh/models/data.json
mxsh models
```

**Reset Shell Preference:**
```bash
rm ~/.mxsh/config/shells.json
mxsh shells
```

**Reset Environment Variables:**
```bash
rm ~/.mxsh/env/store.json
```

## Troubleshooting Configuration Issues

### API Key Not Working

**Symptoms:**
- "API authentication failed" errors
- "API key not found" messages

**Solutions:**

1. Verify API key is valid at [platform.openai.com](https://platform.openai.com)
2. Reinitialize MXSH:
   ```bash
   mxsh init
   ```
3. Check file permissions:
   ```bash
   ls -la ~/.mxsh/credentials/
   ```
   Should be readable by your user.

### Model Configuration Errors

**Symptoms:**
- Commands fail with "model not found"
- Unexpected model behavior

**Solutions:**

1. Verify configuration file:
   ```bash
   cat ~/.mxsh/models/data.json
   ```
2. Ensure model name exactly matches valid options (case-sensitive)
3. Reset to default:
   ```bash
   rm ~/.mxsh/models/data.json
   mxsh models
   ```

### Shell Execution Failures

**Symptoms:**
- Commands not executing
- "Shell not found" errors

**Solutions:**

1. Verify shell is installed:
   ```bash
   which bash
   which powershell
   ```
2. Check shell configuration:
   ```bash
   cat ~/.mxsh/config/shells.json
   ```
3. Ensure `shell_command` matches installed executable
4. Reset shell config:
   ```bash
   rm ~/.mxsh/config/shells.json
   mxsh shells
   ```

### Environment Variable Issues

**Symptoms:**
- Variables not loading
- Decryption errors

**Solutions:**

1. Verify store file exists:
   ```bash
   ls ~/.mxsh/env/store.json
   ```
2. Check file is valid JSON:
   ```bash
   cat ~/.mxsh/env/store.json | python -m json.tool
   ```
3. If corrupted, restore from backup:
   ```bash
   env import backup.mxenv
   ```

### Permission Denied Errors

**Symptoms:**
- Cannot write to `.mxsh` directory
- Configuration changes don't save

**Solutions:**

1. Check directory ownership:
   ```bash
   ls -ld ~/.mxsh/
   ```
2. Fix permissions:
   ```bash
   chmod 700 ~/.mxsh/
   chmod -R 600 ~/.mxsh/*
   ```
3. Ensure you own the directory:
   ```bash
   chown -R $USER:$USER ~/.mxsh/
   ```

## Configuration Best Practices

### Security

1. **Protect the `.mxsh` directory:**
   ```bash
   chmod 700 ~/.mxsh/
   ```
2. **Regular backups of environment variables:**
   ```bash
   env export *
   ```
3. **Use strong passwords for exports:**
   - Minimum 12 characters
   - Mix of letters, numbers, symbols
   - Unique password per export file

4. **Rotate API keys periodically:**
   ```bash
   mxsh init  # Enter new key
   ```

### Performance

1. **Use lightweight models for simple tasks:**
   - Set model to GPT-5 Nano for basic command execution
   - Switch to GPT-5.2 Pro only when Auto Resizer is needed

2. **Leverage whitelisted commands:**
   - Use simple commands without arguments when possible
   - Avoid unnecessary complexity that triggers analysis

### Portability

1. **Document your configuration:**
   - Note which model you're using
   - Record shell preferences
   - Maintain list of environment variables

2. **Use exports for team sharing:**
   ```bash
   env export *
   # Share environment.mxenv with team (securely)
   ```

3. **Version control shell/model configs:**
   ```bash
   # Store in your dotfiles repository
   cp ~/.mxsh/models/data.json ~/dotfiles/mxsh/
   cp ~/.mxsh/config/shells.json ~/dotfiles/mxsh/
   ```

## Environment-Specific Configurations

### Development Environment

```json
{
  "selected_model": "GPT-5 Nano"
}
```
- Fast execution
- Lower costs
- Auto Resizer disabled (consistent behavior)

### Production Environment

Not recommended to use MXSH in automated production environments. MXSH is designed for interactive use with human oversight.

### Learning/Exploration Environment

```json
{
  "selected_model": "GPT-5.2 Pro"
}
```
- Detailed analysis
- Best Auto Resizer performance
- Comprehensive security feedback
