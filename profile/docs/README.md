# MXSH Documentation

Welcome to the MXSH documentation. This guide will help you understand and effectively use MXSH, a universal shell designed to eliminate operating system barriers.

## What is MXSH?

MXSH is a cross-platform command-line shell that enables seamless command execution across different operating systems and shell environments. Rather than forcing users to learn platform-specific syntax, MXSH intelligently translates commands to work on any system while maintaining security through AI-powered validation.

## Core Philosophy

Modern software development often involves working across multiple platforms. Developers switching between Windows, Linux, and macOS face the constant challenge of remembering different command syntaxes, package managers, and shell behaviors. MXSH addresses this friction by providing a unified interface that understands intent rather than demanding specific syntax.

## Key Capabilities

**Universal Command Translation**
Write commands in any shell syntax. MXSH automatically detects the source shell and converts commands to work on your current platform. Use apt commands on Windows, winget on Linux, or brew anywhere.

**AI-Powered Security**
Every non-whitelisted command undergoes AI analysis to assess potential risks. The system evaluates commands for destructive operations, security vulnerabilities, and unintended consequences before execution.

**Encrypted Environment Management**
Store sensitive environment variables with military-grade encryption. Support for both machine-based and password-based encryption ensures your credentials remain secure across sessions.

**Cross-Shell Compatibility**
Execute commands in PowerShell, Bash, or CMD from a single interface. Use shell-specific prefixes or rely on automatic detection to route commands appropriately.

**DNS-Based Download Protection**
Network-based downloads through curl or wget undergo DNS security validation using Cloudflare's malware-blocking resolver, preventing connections to known malicious domains.

## Architecture Overview

MXSH operates as an intelligent intermediary between user input and system execution. When you enter a command, the following pipeline activates:

1. **Command Analysis**: Determine if the command requires shell conversion
2. **Auto Resizer**: Convert cross-platform commands to platform-specific equivalents
3. **Security Validation**: Assess risk level through AI analysis or DNS checking
4. **User Confirmation**: Request approval for medium or high-risk operations
5. **Execution**: Run the validated command in the appropriate shell environment

## Use Cases

**Cross-Platform Development**
Maintain consistent workflows across different operating systems without learning platform-specific commands.

**Security-Conscious Operations**
Prevent accidental execution of dangerous commands through automated risk assessment.

**Team Standardization**
Provide a common interface for teams working across diverse platforms.

**Learning and Exploration**
Safely experiment with unfamiliar commands while receiving real-time security feedback.

## Documentation Structure

- **[Getting Started](getting-started.md)**: Installation, initialization, and first steps
- **[Features](features.md)**: Detailed exploration of MXSH capabilities
- **[Commands](commands.md)**: Complete reference of built-in commands
- **[Security](security.md)**: Understanding MXSH's security model
- **[Configuration](configuration.md)**: Customizing MXSH behavior

## Quick Start

Initialize MXSH with your OpenAI API key:

```bash
mxsh init
```

Execute a command and let MXSH handle the rest:

```bash
apt install nodejs
```

On Windows, MXSH automatically converts this to:

```powershell
winget install nodejs
```

## Requirements

- Operating System: Windows, Linux, or macOS
- OpenAI API key for AI-powered features
- Rust toolchain for building from source

## Community and Support

MXSH is an open-source project. For issues, feature requests, or contributions, visit the [GitHub repository](https://github.com/Mxshellit/Mxsh).

## License

This project is distributed under the terms specified in the repository license file.
