# Zpectre

**Know exactly where your firmware bytes go.**

Zpectre is a fast, zero-dependency ELF binary analyzer built for embedded developers. Track firmware size, catch bloat in CI, and optimize what matters.

```
$ zpectre analyze firmware.elf

=== Zpectre Analysis ===

Architecture: ARM Cortex-M4 (32-bit)
Entry point:  0x08000000

Memory Usage Summary:
  Flash (text + data + rodata): 47.2 KB
  RAM   (data + bss):           12.8 KB

Section Breakdown:
  .text   (code):    38.4 KB
  .rodata (const):    6.2 KB
  .data   (init):     1.8 KB
  .bss    (uninit):  11.0 KB
```

## Features

- **Deep ELF Analysis** — Section sizes, symbol breakdown, memory regions
- **CI Integration** — JSON output, exit codes on threshold breach
- **Size Tracking** — Record sizes at each release, track trends
- **HTML Reports** — Beautiful treemap visualizations
- **Zero Dependencies** — Single static binary, runs anywhere

## Installation

Download the latest release for your platform:

| Platform | Download |
|----------|----------|
| Linux x64 | [zpectre-linux-x64.tar.gz](https://github.com/zpectre/zpectre/releases/latest/download/zpectre-linux-x64.tar.gz) |
| Linux ARM64 | [zpectre-linux-arm64.tar.gz](https://github.com/zpectre/zpectre/releases/latest/download/zpectre-linux-arm64.tar.gz) |
| macOS x64 | [zpectre-macos-x64.tar.gz](https://github.com/zpectre/zpectre/releases/latest/download/zpectre-macos-x64.tar.gz) |
| macOS ARM64 | [zpectre-macos-arm64.tar.gz](https://github.com/zpectre/zpectre/releases/latest/download/zpectre-macos-arm64.tar.gz) |
| Windows x64 | [zpectre-windows-x64.zip](https://github.com/zpectre/zpectre/releases/latest/download/zpectre-windows-x64.zip) |

```bash
# Linux/macOS
curl -L https://github.com/zpectre/zpectre/releases/latest/download/zpectre-linux-x64.tar.gz | tar xz
sudo mv zpectre /usr/local/bin/
```

## Usage

### Basic Analysis

```bash
zpectre analyze firmware.elf
```

### HTML Report

```bash
zpectre analyze firmware.elf --html report.html
```

### CI Integration

```bash
# Fail if flash exceeds 64KB or RAM exceeds 16KB
zpectre analyze firmware.elf --json --max-flash 65536 --max-ram 16384
```

### Track Size Over Releases

```bash
# Record size at each release
zpectre analyze firmware.elf --record v1.0.0
zpectre analyze firmware.elf --record v1.1.0
zpectre analyze firmware.elf --record v1.2.0

# View history
zpectre history
```

### Compare Binaries

```bash
zpectre compare old.elf new.elf
```

## CI Examples

### GitHub Actions

```yaml
- name: Check firmware size
  run: |
    zpectre analyze firmware.elf --json --max-flash 65536 > size.json

- name: Upload size report
  uses: actions/upload-artifact@v3
  with:
    name: firmware-size
    path: size.json
```

### GitLab CI

```yaml
check-size:
  script:
    - zpectre analyze firmware.elf --max-flash 65536 --max-ram 16384
  allow_failure: false
```

## Pro Version

[Zpectre Pro](https://zpectre.dev/pro) adds:

- **Module grouping** — See which libraries consume the most space
- **Trend charts** — Visualize size changes over time
- **Unlimited history** — Track as many releases as you need
- **Priority support**

One-time purchase, lifetime updates. [Get Pro →](https://zpectre.dev/pro)

## License

Proprietary. Free version available for personal and commercial use.

---

Built with Zig. Made for embedded developers.
