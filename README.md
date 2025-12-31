![unshell_hero](./unshell-banner.png)
# Unshell
> The Script Kiddies Nightmare

Effortlessly deobfuscate shell scripts back into source code even with heavenly and multi-layered obfuscation. unshell will search for patterns on shell script, determine and deobfuscate accordingly.

## ⚠️ Testing Branch
This is the **testing branch** with experimental improvements for SSC deobfuscation. For stable version, use the [main branch](https://github.com/Rem01Gaming/unshell).

## What's New in Testing
- **🔧 Improved SSC Deobfuscation**: Replaced unreliable fd/3 reading with robust strace-based syscall interception
- **⚡ Better Reliability**: Works with SSC binaries compiled with recent versions (Dec 2024 - Jan 2025)
- **🛡️ Enhanced Protection Bypass**: Handles segmented decryption (-S flag) and random keys (-r flag)
- **📊 Smarter Detection**: Validates captured scripts and provides better error messages

## Features
- Zero configuration: There's no need for any configuration
- Penetrate: Multi-layered obfuscation is not a problem
- Easy to use: just `unshell -f encrypted1 encrypted2` in cmd
- Fast detection: Pattern-based obfuscation identification

## Supported obfuscation method
<details>
<summary>Shell Script Compiler (SHC)</summary>
SHC works internally called execve to shell, it decrypted at runtimes and visible via command line args process

eg: <code>/bin/sh -c "decrypted shell"</code>
</details>

<details>
<summary>Simple Script Compiler (SSC) - IMPROVED ✨</summary>

SSC uses C++ to encrypt scripts with RC4 cipher and pipes the decrypted content to the interpreter at runtime. 

**New in Testing Branch:**
- Uses `strace` to intercept write() syscalls instead of reading from hardcoded file descriptors
- Handles dynamic pipe allocation properly
- Works with SSC's latest features:
  - Segmented decryption (`-S` flag)
  - Random RC4 keys (`-r` flag)
  - Anti-debugging bypassed via syscall interception
  - CRC32 checksum verification

**Requirements:** `strace`, `timeout`, `awk`, `grep`
</details>

<details>
<summary>Ri-crypt</summary>
Ri-crypt works internally called execve to shell, it decrypted at runtimes and visible via command line args process. we can retrieve the shell script using `strace`.
</details>

<details>
<summary>bash-obfuscate (Node.js CLI)</summary>
bash-obfuscate works by randomizing the script with random variables then execute it in `eval` command.
</details>

<details>
<summary>Bashrock</summary>
Bashrock works almost the same way as bash-obfuscate.
</details>

<details>
<summary>TPP Tool</summary>
The creator of this obfuscation said "it has anti-decode feature" despite multilayered base64 encoding that can be easily decoded.
As of this writing, unshell supports up to version 12 of this "tool".
</details>

<details>
<summary>BashProtector</summary>
BashProtector randomizes the script with random variables layered by single `base64` encryption, then executes it in single `eval` command.
</details>

<details>
<summary>Extreme comment/editor EOF trick</summary>
Some people obfuscate their scripts by adding generous amounts of comments until it becomes a really big file, tricking average text editors into struggling while opening the script.
</details>

<details>
<summary>bzip2</summary>
Usually used for obfuscating tunneling/VPN scripts. The actual script is compressed with bzip2 and embedded inside the decompression script itself.
</details>

<details>
<summary>Axeron online module</summary>
The script is actually stored somewhere online (usually public GitHub pages) and the module only executes the actual script after downloading from cloud. The file link itself is obfuscated with base64 and rot17.
</details>

<details>
<summary>base64</summary>
Not too crazy, just classic <code>echo "ZWNobyBzb21lIGJhc2U2NCBlbmNyeXB0ZWQgc2hpdAo=" | base64 -d | sh</code>.
</details>

<details>
<summary>Kaminari-enc</summary>
Kaminari-enc creates a temporary cache script and executes it at runtime.
</details>

<details>
<summary>putraxitersz</summary>
Putraxitersz uses gzip compression and pipes to shell execution.
</details>

## Installation

### Testing Branch (Improved SSC Support)
```bash
spath=$(echo $PATH | cut -d: -f1)
curl -sLo $spath/unshell https://github.com/hoshiyomiX/unshell/raw/testing/unshell
chmod +x $spath/unshell
```

### Stable Release (Original)
```bash
spath=$(echo $PATH | cut -d: -f1)
curl -sLo $spath/unshell https://github.com/Rem01Gaming/unshell/raw/main/unshell
chmod +x $spath/unshell
```

### Dependencies

**Required:**
- `bash` (obviously)
- `strings` - for binary pattern detection
- `curl` - for downloads
- `grep`, `sed`, `awk` - for text processing

**Optional (for specific obfuscation types):**
- `strace` - for SSC, SHC, Ri-crypt deobfuscation
- `timeout` - for SSC timeout handling
- `shfmt` - for TPP Tool and comment removal
- `bzip2` - for bzip2-obfuscated scripts

Install on Debian/Ubuntu:
```bash
sudo apt install strace coreutils gawk grep sed curl binutils shfmt bzip2
```

Install on Termux:
```bash
pkg install strace coreutils gawk grep sed curl binutils shfmt bzip2
```

## Usage
```yaml
unshell - Deobfuscate any shell scripts with multiple methods
  Usage: unshell [OPTIONS] [FILE]
  Usage: unshell [OPTIONS] [DIR]

  Options:
    -h, --help
      print this message
    -f, --file [FILE]
      Scripts you wanted to deobfuscate, multi input is supported
    -r, --recursive [DIR]
      Recursively find and deobfuscate all files in the specified directory
    -v, --verbose
      Be verbose
    -d, --execve-delay [SECOND]
      Set custom execve delay time in seconds for SHC encryption (SSC no longer uses this)
    -U, --update
      Update the script

  Example usages:
    unshell -f install.sh menu.sh
    unshell -v -f /system/bin/gaming_script
    unshell -d 6.018 -f ./VTK
    unshell -r .
```

### Examples

**Deobfuscate a single file:**
```bash
unshell -f encrypted_script.sh
```

**Deobfuscate multiple files:**
```bash
unshell -f script1.sh script2.sh script3.sh
```

**Deobfuscate all files in current directory:**
```bash
unshell -r .
```

**Verbose mode for debugging:**
```bash
unshell -v -f obfuscated.sh
```

## WARNING
⚠️ Using unshell to retrieve the original shell script from SHC, SSC, or Ri-crypt obfuscation **could potentially harm your machine**. These obfuscation types require executing the script to deobfuscate, which leaves your machine vulnerable if the script does something malicious. 

**Security Recommendations:**
- Avoid running unshell with root/sudo permissions unless you fully trust the script
- Test in isolated environments (containers, VMs) when dealing with unknown scripts
- Review the output before executing deobfuscated scripts
- Be aware that malicious scripts can detect analysis environments

## Technical Details

### SSC Deobfuscation Method
The testing branch uses an improved approach for SSC:

1. **Syscall Interception**: Uses `strace` to capture write() syscalls
2. **Dynamic FD Handling**: No hardcoded file descriptor assumptions
3. **Timeout Protection**: 15-second timeout prevents hanging
4. **Data Extraction**: AWK-based parsing of strace output
5. **Validation**: Checks for valid shell script structure (shebang, syntax)
6. **Cleanup**: Fallback logic to extract scripts from noisy captures

This method works because SSC must write the decrypted script to a pipe before the interpreter can execute it, and strace intercepts this at the kernel level before anti-debugging checks complete.

## Troubleshooting

**SSC deobfuscation fails:**
- Ensure `strace` and `timeout` are installed
- Try verbose mode: `unshell -v -f script.sh`
- Check if binary is heavily protected with `-u` flag (anti-debugging)
- Some SSC binaries with extreme protection may still be difficult to deobfuscate

**Permission denied:**
```bash
chmod +x /path/to/unshell
```

**Command not found:**
```bash
# Make sure installation path is in $PATH
echo $PATH
# Or use absolute path
/usr/local/bin/unshell -f script.sh
```

## Contributing
Contributions are welcome! If you find a new obfuscation method or have improvements:

1. Fork the repository
2. Create a feature branch
3. Add your changes
4. Submit a pull request

## Special Credits
- [kawaii-ghost](https://github.com/kawaii-ghost/deshc) for decsh (shc and ssc deobfuscator)
- [RiProG-id](https://github.com/RiProG-id/Universal-Shell-Dec.git) for universal-shell-dec, the inspiration and foundation of this project
- [Rem01Gaming](https://github.com/Rem01Gaming) for the original unshell project

## Support the Original Author
- [Buy Me a Coffee](https://buymeacoffee.com/rem01gaming)
- [Saweria](https://saweria.co/Rem01Gaming)

## License
GPL-3.0 License - See LICENSE file for details
