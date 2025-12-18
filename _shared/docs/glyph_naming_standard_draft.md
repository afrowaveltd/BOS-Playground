# BOS Font Glyph Storage Standard (Revised)

## 1. Core Principles

- Source text is always **UTF-8**.
- Glyph identity is derived from **Unicode codepoints**, not storage slots.
- File names represent **identity only**, never placement or runtime mapping.
- Slot assignment (e.g. Afwave-ASCII `0x80..0xFF`) is **runtime-only**.
- Naming is **minimal, reversible, and deterministic**.
- No metadata in file names beyond identity.

---

## 2. Directory Structure

```
Letters/
```

Optional style or grouping directories MAY be used (e.g. `afw/`, `unicode/`), but are not required by the standard.

---

## 3. File Naming Rules

### 3.1 Unicode Glyphs

Unicode glyphs are named **only by their hexadecimal codepoint**, without prefixes or redundant padding.

```
<HEX>.glyph
```

Where:

- `<HEX>` is the Unicode codepoint in hexadecimal.
- Uppercase hexadecimal digits (`0–9`, `A–F`) are recommended.
- **No `U+` prefix**.
- **No fixed width or leading zero padding**.
- The name must be directly parseable as a hexadecimal number.

#### Examples

```
41.glyph      ; U+0041 'A'
66.glyph      ; U+0066 'f'
159.glyph     ; U+0159 'ř'
20AC.glyph    ; U+20AC '€'
1F600.glyph   ; U+1F600 😀
```

The filename value is the canonical codepoint representation.

---

### 3.2 Internal / Afwave-Specific Glyphs

Glyphs that do **not** represent Unicode characters (logos, UI icons, symbolic marks) MUST use a leading underscore.

```
_<NAME>.glyph
```

Examples:

```
_AFW_A.glyph
_AFW_f.glyph
_ICON_LOCK.glyph
_UI_ARROW_LEFT.glyph
```

Rules:

- Leading underscore (`_`) marks the glyph as **non-Unicode**.
- These glyphs are resolved by symbolic name, not by codepoint.
- They are not part of Unicode fallback or transliteration logic.

---

## 4. Reversibility

### Unicode Glyphs

```
filename: 159.glyph
→ codepoint = 0x0159
```

```
codepoint = 0x0159
→ filename = "159.glyph"
```

No ambiguity. No normalization step required.

---

## 5. Glyph File Format (Minimal Definition)

This standard does not mandate a specific binary format.

Required properties:

- The file contains **exactly one glyph**.
- Glyph bitmap layout (e.g. `8×16`, `1bpp`) is defined by the consuming context.
- One row MAY correspond to one byte (common case).

Explicitly excluded:

- embedded headers with character names,
- redundant metadata,
- encoding markers.

Semantic meaning is carried **only by the filename**.

---

## 6. Relation to the BOS UTF Pipeline

- UTF-8 input → Unicode codepoint.
- Codepoint → filename `<HEX>.glyph`.
- File exists → glyph available.
- File missing → fallback strategy (transliteration, replacement, ASCII).

Filesystem lookup requires **no lookup tables** and no normalization.

---

## 7. Determinism

- Same codepoint → same filename.
- Same font pack → same glyph resolution.
- Same document → identical byte stream output.

This property is required for:

- caching,
- reproducible builds,
- embedded and minimal systems.

---

## 8. Explicit Non-Goals

- ligatures,
- kerning,
- combining characters,
- Unicode normalization (NFC/NFD).

These concerns are intentionally left to higher layers.