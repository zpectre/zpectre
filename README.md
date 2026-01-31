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
- **HTML Reports** — Beautiful treemap visualizations
- **Compare Binaries** — See what changed between versions
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

### Compare Binaries

```bash
zpectre compare old.elf new.elf
```

## Pro Version

[Zpectre Pro](https://zpectre.dev/pro) unlocks CI integration and advanced features:

- **JSON output** — Machine-readable output for pipelines
- **Size thresholds** — Fail builds when firmware exceeds limits
- **History tracking** — Record and view sizes over releases
- **Trend charts** — Visualize size changes over time
- **Module breakdown** — See which libraries use the most space

### CI Examples (Pro)

```yaml
# GitHub Actions
- name: Check firmware size
  run: zpectre analyze firmware.elf --json --max-flash 65536 > size.json
```

```yaml
# GitLab CI
check-size:
  script:
    - zpectre analyze firmware.elf --max-flash 65536 --max-ram 16384
```

One-time purchase, lifetime updates. [Get Pro →](https://zpectre.dev/pro)

## License

Proprietary. Free version available for personal and commercial use.

---

Built with Zig. Made for embedded developers.
