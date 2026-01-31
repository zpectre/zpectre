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
# Linux/macOS quick install
curl -L https://github.com/zpectre/zpectre/releases/latest/download/zpectre-linux-x64.tar.gz | tar xz
sudo mv zpectre /usr/local/bin/

# Verify installation
zpectre --version
```

## Commands

### `zpectre analyze <file.elf>`

Analyze an ELF binary and display memory usage breakdown.

```bash
zpectre analyze firmware.elf
```

**Output includes:**
- Architecture detection (ARM, RISC-V, x86, etc.)
- Entry point address
- Flash usage (text + data + rodata sections)
- RAM usage (data + bss sections)
- Per-section size breakdown

**Options:**

| Option | Description |
|--------|-------------|
| `--symbols` | Show individual symbol sizes |
| `--top N` | Show top N largest symbols (default: 20) |
| `--html <file>` | Generate HTML treemap report |
| `--json` | Output as JSON (Pro) |
| `--max-flash <bytes>` | Fail if Flash exceeds threshold (Pro) |
| `--max-ram <bytes>` | Fail if RAM exceeds threshold (Pro) |
| `--record <tag>` | Record sizes to history file (Pro) |
| `--modules` | Group symbols by module/library (Pro) |

**Examples:**

```bash
# Basic analysis
zpectre analyze firmware.elf

# Show largest symbols
zpectre analyze firmware.elf --symbols --top 50

# Generate HTML report
zpectre analyze firmware.elf --html report.html

# CI with thresholds (Pro)
zpectre analyze firmware.elf --json --max-flash 65536 --max-ram 16384

# Record to history (Pro)
zpectre analyze firmware.elf --record v1.2.0
```

---

### `zpectre compare <old.elf> <new.elf>`

Compare two ELF binaries and show size differences.

```bash
zpectre compare v1.0.elf v1.1.elf
```

**Output includes:**
- Total size change (Flash and RAM)
- Per-section changes with delta
- Growth/shrink indicators

**Example output:**

```
=== Zpectre Comparison ===

             old.elf    new.elf     delta
Flash:       47.2 KB    52.1 KB    +4.9 KB  (+10.4%)
RAM:         12.8 KB    13.1 KB    +0.3 KB  (+2.3%)

Section Changes:
  .text:     38.4 KB    42.8 KB    +4.4 KB
  .rodata:    6.2 KB     6.7 KB    +0.5 KB
  .data:      1.8 KB     1.8 KB     0.0 KB
  .bss:      11.0 KB    11.3 KB    +0.3 KB
```

---

### `zpectre history` (Pro)

View recorded size history over time.

```bash
zpectre history
```

**Output includes:**
- All recorded versions/tags
- Flash and RAM sizes for each
- Change from previous record

**Example output:**

```
=== Zpectre History ===

Tag          Date         Flash      RAM        Delta
v1.0.0       2025-01-15   42.1 KB    11.2 KB    —
v1.1.0       2025-01-22   45.3 KB    11.8 KB    +3.2 KB
v1.2.0       2025-01-28   47.2 KB    12.8 KB    +1.9 KB
v1.2.1       2025-01-30   46.8 KB    12.8 KB    -0.4 KB
```

**Options:**

| Option | Description |
|--------|-------------|
| `--trend <file>` | Generate HTML trend chart |
| `--json` | Output as JSON |

---

## HTML Reports

Generate interactive treemap visualizations to understand where bytes are spent.

```bash
zpectre analyze firmware.elf --html report.html
```

The HTML report shows:
- Interactive treemap of sections and symbols
- Hover for details on any block
- Color-coded by section type
- Shareable single-file HTML (no dependencies)

---

## Pro Features

[Zpectre Pro](https://zpectre.dev) unlocks CI integration and advanced features:

### JSON Output

Machine-readable output for CI pipelines and scripts.

```bash
zpectre analyze firmware.elf --json
```

```json
{
  "architecture": "ARM Cortex-M4",
  "entry_point": "0x08000000",
  "flash": 48332,
  "ram": 13107,
  "sections": {
    ".text": 39321,
    ".rodata": 6348,
    ".data": 1843,
    ".bss": 11264
  }
}
```

### Size Thresholds

Fail builds when firmware exceeds limits. Returns exit code 1 on breach.

```bash
# Fail if Flash > 64KB or RAM > 16KB
zpectre analyze firmware.elf --max-flash 65536 --max-ram 16384
```

Output on breach:
```
ERROR: Flash size 67584 exceeds threshold 65536 (+2048 bytes)
```

### History Tracking

Record sizes at each release to track growth over time.

```bash
# Record current build
zpectre analyze firmware.elf --record v1.2.0

# View history
zpectre history

# Generate trend chart
zpectre history --trend trends.html
```

History is stored in `.zpectre-history.json` in the current directory.

### Module Breakdown

Group symbols by source module to identify which libraries consume the most space.

```bash
zpectre analyze firmware.elf --modules
```

```
=== Module Breakdown ===

Module                    Flash      RAM
libc                      12.4 KB    2.1 KB
HAL_Driver                 8.2 KB    0.4 KB
FreeRTOS                   6.8 KB    4.2 KB
application                5.1 KB    1.8 KB
USB_Device                 4.3 KB    0.8 KB
...
```

---

## CI Integration Examples

### GitHub Actions

```yaml
name: Firmware Size Check

on: [push, pull_request]

jobs:
  size-check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Install Zpectre
        run: |
          curl -L https://github.com/zpectre/zpectre/releases/latest/download/zpectre-linux-x64.tar.gz | tar xz
          sudo mv zpectre /usr/local/bin/

      - name: Build firmware
        run: make

      - name: Check size limits
        run: zpectre analyze build/firmware.elf --max-flash 65536 --max-ram 16384

      - name: Generate report
        run: zpectre analyze build/firmware.elf --json > size-report.json

      - name: Upload report
        uses: actions/upload-artifact@v4
        with:
          name: size-report
          path: size-report.json
```

### GitLab CI

```yaml
firmware-size:
  stage: test
  script:
    - curl -L https://github.com/zpectre/zpectre/releases/latest/download/zpectre-linux-x64.tar.gz | tar xz
    - ./zpectre analyze build/firmware.elf --max-flash 65536 --max-ram 16384 --json > metrics.json
  artifacts:
    reports:
      metrics: metrics.json
```

### Jenkins

```groovy
pipeline {
    agent any
    stages {
        stage('Size Check') {
            steps {
                sh 'zpectre analyze build/firmware.elf --max-flash 65536'
            }
        }
    }
}
```

---

## Supported Architectures

Zpectre analyzes ELF binaries for any architecture:

- ARM Cortex-M (M0, M0+, M3, M4, M7, M33)
- ARM Cortex-A/R
- RISC-V (RV32, RV64)
- Xtensa (ESP32)
- x86 / x86_64
- AVR
- MSP430
- And any other ELF-based toolchain

---

## Free vs Pro

| Feature | Free | Pro |
|---------|------|-----|
| ELF analysis | ✓ | ✓ |
| Section breakdown | ✓ | ✓ |
| Symbol sizes | ✓ | ✓ |
| HTML treemap reports | ✓ | ✓ |
| Compare two binaries | ✓ | ✓ |
| JSON output | — | ✓ |
| Size thresholds | — | ✓ |
| History tracking | — | ✓ |
| Trend charts | — | ✓ |
| Module breakdown | — | ✓ |
| Priority support | — | ✓ |

**Pro is a one-time purchase with lifetime updates.** [Get Pro →](https://zpectre.dev)

---

## FAQ

**Q: Does Zpectre need debug symbols?**
A: No. Zpectre works with stripped binaries. Debug symbols (DWARF) provide more detail for symbol names but are not required.

**Q: What ELF formats are supported?**
A: Both 32-bit and 64-bit ELF files, little and big endian.

**Q: Where is history stored?**
A: In `.zpectre-history.json` in the current working directory. Commit this to your repo to track sizes across your team.

**Q: Can I use the free version commercially?**
A: Yes. The free version can be used for any purpose, personal or commercial.

**Q: How do I activate Pro?**
A: After purchase, you'll receive a Pro binary. Replace the free binary with the Pro version.

---

## License

Proprietary software. Free version available for personal and commercial use.

---

Built with Zig. Made for embedded developers.

[Website](https://zpectre.dev) · [Releases](https://github.com/zpectre/zpectre/releases) · [Report Issue](https://github.com/zpectre/zpectre/issues)
