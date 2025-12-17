# BOS Font Glyph Storage Standard (Draft)

## 1. Core Principles

- Each Unicode character is represented by **exactly one file**.
- The file name is **uniquely derivable** from the Unicode codepoint.
- The file name is **fully reversible** back to the codepoint.
- No metadata in file names.
- No human-readable labels.
- Pure mechanics.

---

## 2. Directory

```
Letters/
```

No subdirectories are required in the base variant.

---

## 3. File Name

### Mandatory Format

```
U+XXXXXX.ext
```

Where:

- `U+` is a fixed prefix.
- `XXXXXX` is a **six-digit hexadecimal Unicode codepoint**.
- Hexadecimal digits are `0–9` and `A–F` (uppercase only).
- No shortening, no variability.
- `.ext` denotes the glyph file format (e.g. `.bmp`, `.bin`).

### Examples

```
U+000041.bin   ; 'A'
U+000159.bin   ; 'ř'
U+0020AC.bin   ; '€'
U+01F600.bin   ; 😀
```

---

## 4. Mapping Rules

### File Name → Unicode

```
filename: U+000159.bin
→ codepoint = 0x0159
```

### Unicode → File Name

```
codepoint = 0x0159
→ filename = "U+000159.bin"
```

Single rule. No exceptions.

---

## 5. Glyph File Format (Minimal Definition)

This standard does not define a detailed graphical format.

- The file contains a **bitmap of exactly one glyph**.
- Resolution and bit depth are defined by context (e.g. 8×16, 1bpp).
- Content interpretation is the responsibility of the loader.

Intentionally omitted:
- no headers with character names,
- no redundant metadata,
- semantic meaning is carried **only by the file name**.

---

## 6. Relation to the BOS UTF Pipeline

- UTF-8 decoder → codepoint.
- Codepoint → file name `U+XXXXXX.*`.
- File exists → glyph available.
- File missing → fallback (transliteration / similar character / ASCII).

No lookup tables are required at the filesystem level.

---

## 7. Determinism

- Same character → same file.
- Same document → same glyph selection.
- Same font pack → same byte stream.

This is essential for:
- caching,
- embedded systems,
- reproducible builds.

---

## 8. Explicit Non-Goals

- ligatures,
- kerning,
- combining characters,
- Unicode normalization (NFC/NFD).

These are considered higher-level concerns and are intentionally exclude