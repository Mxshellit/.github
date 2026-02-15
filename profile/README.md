# MXSH

[![Deploy](https://github.com/Mxshellit/Mxsh/actions/workflows/deploy.yml/badge.svg)](https://github.com/Mxshellit/Mxsh/actions/workflows/deploy.yml)

A universal shell that eliminates operating system barriers through intelligent command translation and AI-powered security validation.  

https://github.com/user-attachments/assets/8b772b02-8f82-4518-9a35-a6e51bdce690

## Overview

MXSH provides a unified command-line interface across different operating systems with intelligent security validation. Commands are analyzed by AI before execution to ensure safety and reliability. The system supports multiple shells and includes comprehensive security features to protect against malicious operations.

## Key Features

**Multi-Shell Support with Shell Swapping**
Execute commands in PowerShell, CMD, Zsh, or Bash from a single interface. Switch between shells seamlessly using prefixes or configure your default shell preference.

**AI-Based Command Filtering and Inspection**
Every command is analyzed through AI-powered security validation before execution. The system evaluates risk levels, detects potentially destructive operations, and ensures only trusted commands run on your system.

**DNS-Based Malicious Download Prevention**
Network downloads are validated against Cloudflare's malware-blocking DNS resolver. The system prevents connections to known malicious domains when using tools like curl or wget.

**Network Connectivity Diagnostics**
The netcheck feature provides comprehensive internet connection testing from your router through external DNS servers to destination hosts, helping diagnose network issues quickly.

**AES-Encrypted Environment Variable Management**
Store and share sensitive environment variables securely using AES-256-GCM encryption. Supports both machine-specific and password-based encryption for flexible credential management.

**GitHub Device Flow Authentication and Backup**
Authenticate users through GitHub Device Flow and back up your configuration securely. This enables seamless setup across multiple machines while maintaining security.

## Installation

Build from source using the Rust toolchain:

```bash
git clone https://github.com/Mxshellit/Mxsh.git
cd Mxsh
cargo build --release
```

After building, initialize MXSH with your OpenAI API key:

```bash
mxsh init
```

## Usage

Run commands naturally and MXSH will handle platform conversion and security validation:

```bash
apt install nodejs
```

On Windows, this automatically converts to `winget install nodejs`. On macOS, it converts to `brew install nodejs`.

## Requirements

- Operating System: Windows, Linux, or macOS
- Rust toolchain for building from source
- OpenAI API key for AI-powered command validation

## Built-In Commands

MXSH includes several built-in commands for configuration and system management:

- `help` - Display available commands and usage information
- `init` - Initialize MXSH configuration and set up API keys
- `whoami` - Show current user information and operating system
- `models` - Select and configure AI model preferences
- `shells` - Configure default shell and shell preferences
- `cpu` - Display top processes sorted by CPU usage
- `cd` - Navigate directories with enhanced path handling
- `env` - Manage encrypted environment variables
- `netcheck` - Diagnose network connectivity from router to destination
- `clear` - Clear screen and redisplay MXSH banner

## Shell Execution

Execute commands in specific shells using prefixes:

```bash
>ps: Get-Process       # PowerShell
>zsh: ps aux             # Zsh
>cmd: dir /w            # CMD
```

Without a prefix, MXSH uses your configured default shell or automatically selects based on the command syntax.

## Security Model

MXSH implements a multi-layered security approach to protect your system:

**Risk Classification**
Commands are analyzed and classified as SAFE, LOW, MEDIUM, HIGH, or CRITICAL based on potential system impact. High-risk operations require explicit user confirmation before execution.

**Command Whitelisting**
Common read-only commands such as ls, cat, pwd, and git execute immediately without AI analysis for better performance.

**DNS Validation**
Network downloads are checked against malware databases before allowing connections to external hosts.

**Security Bypass Options**
For trusted automation or known-safe operations, you can use:
- `--skip-ai-check` - Bypass AI security validation
- `--skip-dns-check` - Skip DNS malware checking for downloads

## License

This project is released under the terms specified in the LICENSE file.

## Support

For bug reports, feature requests, or questions, please visit the [GitHub repository](https://github.com/Mxshellit/Mxsh/issues). 
