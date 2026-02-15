# MXSH

[![Deploy](https://github.com/Mxshellit/Mxsh/actions/workflows/deploy.yml/badge.svg)](https://github.com/Mxshellit/Mxsh/actions/workflows/deploy.yml)

A universal shell that eliminates operating system barriers through intelligent command translation and AI-powered security validation.  


<img width="1920" height="1080" alt="mxsh" src="https://github.com/user-attachments/assets/cafb139a-1f0b-4316-8322-1208695d187c" />

## Overview

MXSH is a cross-platform command-line shell designed to provide a unified interface across Windows, Linux, and macOS. Instead of requiring users to learn platform-specific command syntax, MXSH automatically detects and converts commands to work on any system while maintaining security through comprehensive validation.

## Key Features

**Universal Command Translation**
Execute commands in any shell syntax. MXSH automatically converts platform-specific commands to work on your current system. Use `apt` on Windows, `winget` on Linux, or `brew` anywhere.

**AI-Powered Security Analysis**
Every command undergoes intelligent risk assessment before execution. The system evaluates potential dangers, destructive operations, and security implications through OpenAI's API.

**DNS-Based Download Protection**
Network downloads via `curl` or `wget` are validated against Cloudflare's malware-blocking DNS resolver, preventing connections to known malicious domains.

**Encrypted Environment Management**
Store sensitive environment variables with AES-256-GCM encryption. Support for both machine-specific and password-based encryption ensures credential security.

**Multi-Shell Support**
Execute commands in PowerShell, Bash, or CMD from a single interface using shell-specific prefixes or automatic routing.

## Quick Start

### Installation

Build from source using Rust:

```bash
git clone https://github.com/Mxshellit/Mxsh.git
cd Mxsh
cargo build --release
```

### Initialize

Set up your OpenAI API key:

```bash
mxsh init
```

### Use

Run commands naturally and let MXSH handle the rest:

```bash
apt install nodejs
```

On Windows, MXSH automatically converts this to `winget install nodejs`.

## Documentation

Comprehensive documentation is available in the `docs/` directory:

- **[Introduction](docs/README.md)** - Overview and core concepts
- **[Getting Started](docs/getting-started.md)** - Installation and setup guide
- **[Features](docs/features.md)** - Detailed feature documentation
- **[Commands](docs/commands.md)** - Complete command reference
- **[Security](docs/security.md)** - Security model and best practices
- **[Configuration](docs/configuration.md)** - Customization and settings

## Requirements

- Operating System: Windows, Linux, or macOS
- Rust toolchain (for building from source)
- OpenAI API key (for AI-powered features)

## Built-In Commands

- `help` - Display command reference
- `init` - Initialize MXSH configuration
- `whoami` - Show current user and OS
- `models` - Select AI model
- `shells` - Configure default shell
- `cpu` - Display top processes by CPU usage
- `cd` - Enhanced directory navigation
- `env` - Manage encrypted environment variables
- `clear` - Clear screen and redisplay banner

## Shell Prefixes

Execute commands in specific shells:

```bash
$: Get-Process       # PowerShell
#: ps aux             # Bash
>: dir /w            # CMD
```

## Security Features

**Risk Levels**
Commands are classified as SAFE, LOW, MEDIUM, HIGH, or CRITICAL based on potential impact. High-risk operations require explicit user confirmation or bypass flags.

**Whitelisted Commands**
Common read-only commands like `ls`, `cat`, and `git` execute immediately without AI analysis.

**Bypass Flags**
For trusted operations:
- `--skip-ai-check` - Bypass AI security analysis
- `--skip-dns-check` - Skip DNS validation for downloads

## Contributing

Contributions are welcome. Please refer to the repository's contribution guidelines for details on submitting issues, feature requests, and pull requests.

## License

See the LICENSE file for details.

## Support

For issues and questions, visit the [GitHub repository](https://github.com/Mxshellit/Mxsh). 
