# Version History

This document tracks the Hermes Bytecode format changes across supported hbctool versions.

## HBC Version 96

**hbctool support added:** v0.2.0  
**Hermes release era:** React Native 2026 (latest)

### Changes from v94

- `BytecodeOptions` gains the `hasAsync` bit flag:
  ```cpp
  union BytecodeOptions {
    struct {
      bool staticBuiltins : 1;
      bool cjsModulesStaticallyResolved : 1;
      bool hasAsync : 1;   // NEW
    };
    uint8_t _flags;
  };
  ```
- Header total size unchanged (124 bytes)
- No new opcodes relative to v94

---

## HBC Version 94

**hbctool support added:** v0.2.0  
**Hermes release era:** React Native 2025 stable

### Changes from v90

- Minor `BytecodeOptions` struct refinements
- Header layout identical to v90
- No new opcodes relative to v90

---

## HBC Version 90

**hbctool support added:** v0.2.0  
**Hermes release era:** React Native 2024–2025

### Changes from v85

#### Header fields (all uint32 unless noted)

| Field             | Change                                          |
|-------------------|-------------------------------------------------|
| `bigIntCount`     | **NEW** — number of BigInt literals             |
| `bigIntStorageSize` | **NEW** — byte size of BigInt storage block   |
| `cjsModuleOffset` | **RENAMED** to `segmentID`                      |
| `functionSourceCount` | **NEW** — number of preserved function sources |
| `padding`         | Reduced from 31 bytes to 19 bytes               |

Header total size remains 124 bytes.

#### New file segments

- **BigInt Table** — array of `{offset: uint32, length: uint32}` entries (one per BigInt literal)
- **BigInt Storage** — raw byte blob containing serialised BigInt data
- **Function Source Table** — array of `{first: uint32, second: uint32}` pairs (functionID → stringID mapping for preserved source)

#### New opcodes

Two opcodes inserted after `LoadConstDouble` (opcode index 110):

| Opcode                    | Operands           | Byte value |
|---------------------------|--------------------|------------|
| `LoadConstBigInt`         | `Reg8`, `UInt16`   | 111        |
| `LoadConstBigIntLongIndex`| `Reg8`, `UInt32`   | 112        |

All subsequent opcodes (previously starting at 111) are shifted up by 2.

---

## HBC Version 85

**hbctool support added:** v0.1.5  
**Hermes release era:** React Native 2023–2024

### Notable fields (relative to v84)

- Generator and async closure opcodes (`CreateGeneratorClosure`, `CreateAsyncClosure`, etc.)
- `SaveGenerator` jump instruction

---

## HBC Version 84

**hbctool support added:** v0.1.4  
**Hermes release era:** React Native 2023

---

## HBC Version 76

**hbctool support added:** v0.1.x  
**Hermes release era:** Legacy

---

## HBC Version 74

**hbctool support added:** v0.1.x  
**Hermes release era:** Legacy

---

## HBC Version 62

**hbctool support added:** v0.1.x  
**Hermes release era:** Legacy

---

## HBC Version 59

**hbctool support added:** v0.1.0  
**Hermes release era:** Original Hermes release

---

## Header Size Reference

All supported versions use a 124-byte file header. The total size is maintained by adjusting the `padding` field as new fields are added.

| Version | uint32 fields | option (uint8) | padding (bytes) | Total |
|---------|--------------|----------------|-----------------|-------|
| 59–85   | 15           | 1              | 31              | 124   |
| 90–96   | 18           | 1              | 19              | 124   |
