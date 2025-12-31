![unshell_hero](./unshell-banner.png)
# Unshell
> The Script Kiddies Nightmare

Effortlessly deobfuscate shell scripts back into source code even with heavenly and multi-layered obfuscation. This tool automatically detects obfuscation methods and applies the appropriate deobfuscation technique.

## ⚠️ Testing Branch
This is the **testing branch** with experimental improvements. For the stable version, use the [main branch](https://github.com/Rem01Gaming/unshell).

## What's New in This Fork
- **🔧 Improved SSC Deobfuscation**: Replaced unreliable fd/3 reading with robust strace-based syscall interception
- **⚡ Better Reliability**: Works with SSC binaries compiled with recent versions (Dec 2024 - Jan 2025)
- **🛡️ Enhanced Protection Bypass**: Handles segmented decryption (`-S` flag) and random keys (`-r` flag)
- **📊 Smarter Detection**: Validates captured scripts with better error messages
- **🆕 Generic Binary Handler**: Automatically handles stripped ELF binaries and unknown compilers
- **🎯 Multi-Method Fallback**: If one deobfuscation method fails, automatically tries alternatives

## Features
- **Zero configuration**: No setup required, works out of the box
- **Multi-layered penetration**: Handles scripts obfuscated multiple times
- **Easy to use**: Simple command-line interface: `unshell -f script1 script2`
- **Pattern-based detection**: Automatically identifies obfuscation methods
- **Smart fallback**: Handles unknown compilers and stripped binaries automatically
- **Batch processing**: Process multiple files or entire directories recursively

## Supported Obfuscation Methods

<details>
<summary><b>Shell Script Compiler (SHC)</b></summary>

SHC encrypts scripts and decrypts them at runtime via execve() syscalls. The decrypted script is visible in the process command line.

**Detection method**: String pattern `neither argv[0] nor $_ works`  
**Deobfuscation**: Process memory capture via `/proc/{pid}/cmdline`  
**Requirements**: Standard Linux tools
</details>

<details>
<summary><b>Simple Script Compiler (SSC)</b> - IMPROVED ✨</summary>

SSC uses C++ and RC4 cipher to encrypt scripts, piping decrypted content to the interpreter at runtime.

**What's improved:**
- Strace-based syscall interception (replaces unreliable fd reading)
- Dynamic pipe allocation handling
- Support for SSC's latest features:
  - Segmented decryption (`-S` flag)
  - Random RC4 keys (`-r` flag)
  - Anti-debugging bypass via kernel-level interception
  - CRC32 checksum verification

**Requirements**: `strace`, `timeout`, `awk`, `grep`
</details>

<details>
<summary><b>Generic Binary (Stripped/Unknown Compilers)</b> - NEW 🆕</summary>

Intelligent fallback handler for ELF binaries without recognizable signatures.

**Supports:**
- Stripped SSC/SHC binaries (no debug symbols)
- Custom shell script compilers
- Binaries compiled with Android NDK r29+
- Unknown proprietary obfuscation tools

**How it works:**
1. Detects ELF executables with shell wrapper characteristics
2. Searches for syscall patterns: `pipe2`, `fork`, `execvp`, `environ`
3. Tries multiple capture methods:
   - Write() syscall interception (SSC-style)
   - Execve() cmdline capture (SHC-style)
4. Validates and extracts decrypted scripts

**Requirements**: `strace`, `timeout`, `file`
</details>

<details>
<summary><b>Ri-crypt</b></summary>

Ri-crypt works similarly to SHC, using execve() to shell with runtime decryption.

**Deobfuscation**: Strace-based execve monitoring with eval extraction
</details>

<details>
<summary><b>bash-obfuscate</b> (Node.js CLI)</summary>

Randomizes scripts with random variable names and executes via `eval` command.

**Detection**: Pattern matching for randomized variable structures
</details>

<details>
<summary><b>Bashrock</b></summary>

Similar to bash-obfuscate with variable randomization and eval execution.

**Detection**: `$RzE` pattern signature
</details>

<details>
<summary><b>TPP Tool</b></summary>

Claims "anti-decode" protection despite using simple multilayered base64 encoding.

**Support**: Up to version 12  
**Method**: Pattern extraction and base64 decoding
</details>

<details>
<summary><b>BashProtector</b></summary>

Randomizes scripts with base64 encryption layer and single eval execution.

**Detection**: `Tx=Eds` signature pattern
</details>

<details>
<summary><b>Extreme comment/EOF trick</b></summary>

Obfuscates by adding massive amounts of comments to create large files that overwhelm text editors.

**Detection**: Comment count threshold (>180 lines)  
**Method**: Comment removal via shfmt
</details>

<details>
<summary><b>bzip2</b></summary>

Commonly used for tunneling/VPN scripts. Compresses actual script with bzip2 and embeds it in a decompression wrapper.

**Method**: Skip header extraction and bzip2 decompression
</details>

<details>
<summary><b>Axeron Online Module</b></summary>

Stores scripts remotely (usually GitHub pages) with obfuscated download links using base64 and rot17.

**Method**: Link deobfuscation and remote script retrieval
</details>

<details>
<summary><b>base64</b></summary>

Classic base64 encoding with pipe-to-shell execution.

**Example**: `echo "base64data" | base64 -d | sh`
</details>

<details>
<summary><b>Kaminari-enc</b></summary>

Creates temporary cache scripts and executes them at runtime.

**Detection**: `/data/local/tmp/.cache_script.sh` pattern
</details>

<details>
<summary><b>putraxitersz</b></summary>

Uses gzip compression with pipe-to-shell execution.

**Detection**: Modul signature pattern
</details>

## Installation

### Quick Install (Recommended)
```bash
spath=$(echo $PATH | cut -d: -f1)
curl -sLo $spath/unshell https://github.com/hoshiyomiX/unshell/raw/testing/unshell
chmod +x $spath/unshell
```

### Termux
```bash
curl -sLo $PREFIX/bin/unshell https://github.com/hoshiyomiX/unshell/raw/testing/unshell
chmod +x $PREFIX/bin/unshell
```

### Original Stable Version
For the original stable release by Rem01Gaming:
```bash
spath=$(echo $PATH | cut -d: -f1)
curl -sLo $spath/unshell https://github.com/Rem01Gaming/unshell/raw/main/unshell
chmod +x $spath/unshell
```

## Dependencies

### Required
- `bash` - Shell interpreter
- `strings` - Binary pattern detection
- `curl` - Downloads and updates
- `grep`, `sed`, `awk` - Text processing
- `file` - Binary type detection

### Optional (for specific obfuscation types)
- `strace` - SSC, SHC, Ri-crypt, and generic binary deobfuscation
- `timeout` - SSC timeout handling (part of `coreutils`)
- `shfmt` - TPP Tool and comment removal
- `bzip2` - bzip2-obfuscated scripts

### Install Dependencies

**Debian/Ubuntu:**
```bash
sudo apt install strace coreutils gawk grep sed curl binutils file shfmt bzip2
```

**Termux:**
```bash
pkg install strace coreutils gawk grep sed curl binutils file shfmt bzip2
```

## Usage

```
unshell - Deobfuscate any shell scripts with multiple methods
  Usage: unshell [OPTIONS] [FILE]
  Usage: unshell [OPTIONS] [DIR]

  Options:
    -h, --help
      print this message
    -f, --file [FILE]
      Scripts you want to deobfuscate, multiple files supported
    -r, --recursive [DIR]
      Recursively find and deobfuscate all files in directory
    -v, --verbose
      Enable verbose output for debugging
    -d, --execve-delay [SECOND]
      Custom execve delay for SHC encryption (decimal values supported)
    -U, --update
      Update to the latest version
```

## Examples

**Single file:**
```bash
unshell -f encrypted_script.sh
```

**Multiple files:**
```bash
unshell -f script1.sh script2.sh script3.sh
```

**Recursive directory processing:**
```bash
unshell -r /path/to/scripts/
```

**Current directory:**
```bash
unshell -r .
```

**Verbose mode (debugging):**
```bash
unshell -v -f obfuscated.sh
```

**Stripped Android binary:**
```bash
unshell -f /system/bin/custom_script
```

**Custom SHC delay:**
```bash
unshell -d 0.5 -f shc_encrypted.sh
```

**Update:**
```bash
unshell -U
```

## ⚠️ Security Warning

Using unshell to deobfuscate scripts from SHC, SSC, Ri-crypt, or unknown binaries **requires executing the obfuscated code**. This poses security risks if the script is malicious.

### Security Best Practices

✅ **DO:**
- Use isolated environments (containers, VMs) for unknown scripts
- Review deobfuscated output before execution
- Avoid root/sudo unless you fully trust the script
- Test in sandboxed environments first

❌ **DON'T:**
- Run unshell as root on untrusted scripts
- Execute deobfuscated scripts without review
- Process scripts from untrusted sources on production systems
- Ignore warnings about potentially malicious code

## Technical Details

### SSC Deobfuscation Method

**Traditional approach (unreliable):**
- Hardcoded file descriptor reading (fd/3)
- Fails with dynamic allocation
- Broken by recent SSC updates

**Our improved approach:**
1. **Syscall Interception**: Strace captures write() syscalls at kernel level
2. **Dynamic FD Handling**: No hardcoded assumptions about file descriptors
3. **Timeout Protection**: 15-second timeout prevents infinite hangs
4. **Data Extraction**: AWK-based parsing of strace output
5. **Validation**: Checks for valid shell script structure (shebang, syntax)
6. **Cleanup**: Fallback logic extracts scripts from noisy captures

**Why it works:**  
SSC must write decrypted scripts to pipes before execution. Strace intercepts at kernel level before anti-debugging checks complete.

### Generic Binary Handler

**Detection criteria:**
- ELF executable format
- Shell wrapper indicators: `pipe2`, `fork`, `execvp`, `environ`

**Deobfuscation workflow:**
1. **Primary**: Write() syscall capture (SSC-style)
2. **Fallback**: Execve() cmdline capture (SHC-style)
3. **Validation**: Ensures valid shell script output

**Supported binaries:**
- Android NDK compiled wrappers
- Stripped binaries (no symbols)
- Custom script compilers
- Proprietary obfuscation tools
- Modified SSC/SHC variants

## Troubleshooting

### SSC deobfuscation fails

**Symptoms:**
- "Failed to capture script" error
- Empty output file
- Timeout after 15 seconds

**Solutions:**
1. Install `strace` and `timeout`:
   ```bash
   pkg install strace coreutils  # Termux
   sudo apt install strace coreutils  # Debian/Ubuntu
   ```
2. Enable verbose mode: `unshell -v -f script.sh`
3. Check for advanced anti-debugging (`-u` flag)
4. Some heavily protected binaries may be uncrackable

### Generic binary handler fails

**Symptoms:**
- "ELF binary detected but doesn't appear to be a shell script wrapper"
- Handler activates but produces no output

**Solutions:**
1. Verify binary has shell patterns:
   ```bash
   strings binary | grep -E 'pipe|fork|exec'
   ```
2. Try custom delay: `unshell -d 0.8 -f binary`
3. Check if `file` command detects ELF properly
4. Binary might use custom IPC (not standard pipes)

### Command not found

```bash
# Check installation
which unshell

# Verify $PATH
echo $PATH

# Use absolute path
/usr/local/bin/unshell -f script.sh
```

### Permission denied

```bash
# Fix permissions
chmod +x $(which unshell)

# Or
sudo chmod +x /usr/local/bin/unshell
```

### Strace permission denied (Android/Termux)

Android 10+ restricts ptrace. Solutions:
- Root device and run as root
- Use older Android version
- Some methods work without root (depends on binary)

## Performance

- **Text-based obfuscation**: Instant (< 1 second)
- **SSC/SHC binaries**: 1-15 seconds (timeout protection)
- **Generic binaries**: 1-15 seconds (tries multiple methods)
- **Multi-layered**: Depends on layer count (automatic)

## Contributing

Contributions welcome! To add new obfuscation methods:

1. Fork this repository
2. Create feature branch: `git checkout -b feature/new-obfuscation`
3. Add detection pattern in `init_dec()` function
4. Implement `decrypt_yourmethod()` function
5. Test thoroughly
6. Submit pull request

**What to include:**
- Pattern detection logic
- Deobfuscation function
- README documentation
- Test samples (if possible)

## Credits

- **[kawaii-ghost](https://github.com/kawaii-ghost/deshc)** - decsh (shc and ssc deobfuscator)
- **[RiProG-id](https://github.com/RiProG-id/Universal-Shell-Dec.git)** - universal-shell-dec (inspiration and foundation)
- **[Rem01Gaming](https://github.com/Rem01Gaming)** - Original unshell project

## Support

Support the original author:
- [Buy Me a Coffee](https://buymeacoffee.com/rem01gaming)
- [Saweria](https://saweria.co/Rem01Gaming)

## License

GPL-3.0 License - See [LICENSE](LICENSE) file for details

---

**⚡ Maintained by [hoshiyomiX](https://github.com/hoshiyomiX)**  
**⭐ Original by [Rem01Gaming](https://github.com/Rem01Gaming)**
