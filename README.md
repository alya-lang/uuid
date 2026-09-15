# uuid

[![CI](https://github.com/alya-lang/uuid/actions/workflows/ci.yml/badge.svg)](https://github.com/alya-lang/uuid/actions/workflows/ci.yml)
[![License](https://img.shields.io/github/license/alya-lang/uuid?color=blue&label=License)](LICENSE)
[![Alya](https://img.shields.io/badge/dynamic/toml?url=https%3A%2F%2Fraw.githubusercontent.com%2Falya-lang%2Fuuid%2Fmain%2Falya.toml&query=%24.package.alya-version&label=Alya&color=orange&prefix=%3E%3D)](https://github.com/alya-lang/alya)
[![Package Version](https://img.shields.io/badge/dynamic/toml?url=https%3A%2F%2Fraw.githubusercontent.com%2Falya-lang%2Fuuid%2Fmain%2Falya.toml&query=%24.package.version&label=Version&color=brightgreen)](alya.toml)

RFC 4122 UUID v4, RFC 9562 UUID v7, ULID, and NanoID parsing, generation, and validation for Alya.

---

## 🌟 Features

- 🆔 **UUID Version 4**: RFC 4122 compliant pseudo-random 128-bit identifier generation (hyphenated standard and 32-char simple hex).
- ⏱️ **UUID Version 7**: RFC 9562 timestamp-ordered identifier generation with millisecond resolution for high-performance database indexing.
- 🗂️ **ULID**: 26-character Crockford Base32 Universally Unique Lexicographically Sortable Identifier with millisecond timestamp decoding.
- 🔤 **NanoID**: Compact, URL-friendly unique string identifier with custom alphabet support.
- 🔍 **Parsing & Inspection**: Format validation (`is_valid`), version detection (`version`), millisecond timestamp extraction (`timestamp`), and structured inspection (`parse`).
- 🔄 **Conversions & Formats**: 16-byte array serialization (`to_bytes`, `from_bytes`), URN formatting (`to_urn`), Nil (`nil`), and Max (`max`) constants.

---

## 📁 Project Architecture

```
uuid/
├── alya.toml              # Package manifest
├── alya.lock              # Locked dependency tree
├── src/
│   ├── lib.alya           # Public API facade
│   ├── types.alya         # Uuid & UlidInfo structs, Nil & Max constants
│   └── core/
│       ├── v4.alya        # RFC 4122 UUID v4 generator
│       ├── v7.alya        # RFC 9562 UUID v7 generator (Time-ordered)
│       ├── ulid.alya      # Crockford Base32 ULID generator & decoder
│       ├── nanoid.alya    # Compact URL-safe NanoID generator
│       └── parse.alya     # Validation, parsing, byte conversion, URN format
├── examples/
│   └── demo.alya          # Runnable showcase
├── tests/
│   └── test_basic.alya    # Comprehensive test suite
└── benches/
    └── bench_basic.alya   # Micro-benchmarks
```

---

## 📦 Installation

Add `uuid` to your `alya.toml` dependencies:

```toml
[dependencies]
uuid = { git = "https://github.com/alya-lang/uuid", branch = "main" }
```

Or install it directly using the Alya package CLI:

```bash
alyac add uuid --git https://github.com/alya-lang/uuid --branch main
alyac install
```

---

## 🚀 Quick Start

```alya
import "uuid"

function main()
    # 1. UUID v4 (Random)
    let u4 = uuid::v4()
    say "UUID v4: " + u4

    # 2. UUID v7 (Time-Ordered)
    let u7 = uuid::v7()
    say "UUID v7: " + u7
    say "Extracted timestamp: " + str(uuid::timestamp(u7))

    # 3. Validation & Version
    if uuid::is_valid(u7)
        say "Version: " + str(uuid::version(u7))
    end

    # 4. Byte Conversion
    let bytes = uuid::to_bytes(u4)
    let restored = uuid::from_bytes(bytes)

    # 5. ULID & NanoID
    let my_ulid = uuid::ulid()
    let my_nanoid = uuid::nanoid(21)
end

main()
```

---

## 📖 API Reference

### Generation

| Function | Arguments | Returns | Description |
|---|---|---|---|
| `v4()` | - | `string` | Generates standard 36-character RFC 4122 UUID v4. |
| `v4_simple()` | - | `string` | Generates 32-character hex UUID v4 without hyphens. |
| `v7()` | - | `string` | Generates standard 36-character RFC 9562 UUID v7 using current time. |
| `v7_at(timestamp_ms)` | `timestamp_ms: int` | `string` | Generates standard 36-character UUID v7 for given millisecond epoch time. |
| `v7_simple()` | - | `string` | Generates 32-character hex UUID v7 without hyphens using current time. |
| `v7_simple_at(timestamp_ms)` | `timestamp_ms: int` | `string` | Generates 32-character hex UUID v7 without hyphens for given timestamp. |
| `ulid()` | - | `string` | Generates 26-character Crockford Base32 ULID. |
| `ulid_at(timestamp_ms)` | `timestamp_ms: int` | `string` | Generates 26-character Crockford Base32 ULID for given timestamp. |
| `nanoid(size = 21, alphabet = ...)` | `size: int, alphabet: string` | `string` | Generates URL-friendly unique identifier string. |

### Validation & Parsing

| Function | Arguments | Returns | Description |
|---|---|---|---|
| `is_valid(s)` | `s: string` | `int` | Returns `1` if `s` is a valid 36-char or 32-char UUID, else `0`. |
| `is_nil(s)` | `s: string` | `int` | Returns `1` if `s` is the Nil UUID, else `0`. |
| `is_max(s)` | `s: string` | `int` | Returns `1` if `s` is the Max UUID, else `0`. |
| `version(s)` | `s: string` | `int` | Extracts UUID version (`4`, `7`, etc.). Returns `0` if invalid. |
| `timestamp(s)` | `s: string` | `int` | Extracts 48-bit millisecond Unix timestamp from UUID v7. Returns `0` if not v7. |
| `ulid_timestamp(s)` | `s: string` | `int` | Decodes 48-bit millisecond timestamp from ULID string. |
| `ulid_is_valid(s)` | `s: string` | `int` | Returns `1` if `s` is a valid 26-char Crockford Base32 ULID. |
| `parse(s)` | `s: string` | `Uuid` | Parses string into structured `Uuid` struct (`raw`, `version`, `is_valid`, `bytes`). |

### Conversions & Formatting

| Function | Arguments | Returns | Description |
|---|---|---|---|
| `to_bytes(s)` | `s: string` | `array` | Converts UUID string to array of 16 byte integers (0..255). |
| `from_bytes(bytes)` | `bytes: array` | `string` | Constructs standard 36-character UUID from 16 byte integers. |
| `to_simple(s)` | `s: string` | `string` | Strips hyphens to return 32-character hexadecimal string. |
| `to_standard(s)` | `s: string` | `string` | Formats 32-character hex string into 36-character standard `8-4-4-4-12`. |
| `to_urn(s)` | `s: string` | `string` | Formats UUID as RFC 4122 URN (`urn:uuid:...`). |
| `nil_uuid()` | - | `string` | Returns the Nil UUID (`00000000-0000-0000-0000-000000000000`). |
| `max_uuid()` | - | `string` | Returns the Max UUID (`ffffffff-ffff-ffff-ffff-ffffffffffff`). |

---

## 🧪 Running Tests & Benchmarks

Run the test suite using `alyac`:

```bash
alyac run tests/test_basic.alya
```

Run the benchmark suite:

```bash
alyac run benches/bench_basic.alya
```

Run the example demo:

```bash
alyac run examples/demo.alya
```

---

## 🤝 Contributing

Contributions are welcome! Please follow these steps:

1. Fork the repository and clone it locally
2. Install dependencies:
   ```bash
   alyac install
   ```
3. Create your feature branch (`git checkout -b feature/my-feature`)
4. Verify tests and formatting before opening a PR:
   ```bash
   alyac test
   ```
5. Commit your changes (`git commit -m "feat: add feature"`) and open a Pull Request

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
