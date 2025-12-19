# Glyph Directory Structure and Usage

This document extends the **Glyph Naming Standard** and defines how glyph files are organized on disk, how folders are interpreted by the system, and how fallback resolution works. It is written primarily for **BOS** and related Afrowave systems, but is applicable to any runtime or tool using the Afrowave glyph format.

---

## 1. Terminology

- **Glyph** – A single symbol stored as a bitmap in a `.glyph` file.
- **Symbol Pack** – A collection of glyphs designed for a specific grid size.
- **Style / Layer** – A folder representing an overlay set of glyphs (bold, decorative, emojis, etc.).
- **Base Layer** – The guaranteed fallback layer containing the core symbol set.

---

## 2. Root Folder

All glyphs are stored under a single root folder:

```
Symbols/
```

This folder is considered the **entry point** for symbol resolution. The system never mixes glyphs from different grid sizes.

---

## 3. Symbol Pack Folders

Each symbol pack is stored in its own subfolder. The recommended and primary naming scheme is:

```
<WIDTH>x<HEIGHT>/
```

Example:

```
Symbols/
  8x16/
  16x16/
```

Each pack contains glyphs designed **exclusively** for that grid size. Mixing grid sizes in a single pack is not allowed.

---

## 4. Style and Layer Folders

Inside each pack folder, glyphs are organized into **style layers**, represented as subfolders.

### 4.1 Base Layer (`_base_`)

Every pack **must** contain a folder named:

```
_base_/
```

The `_base_` layer has a special meaning:

- It is the **default and fallback layer** for all symbol resolution.
- It contains the **minimum guaranteed glyph set** required by the system.
- In BOS, this corresponds to the core symbol set (for example, the ~432 guaranteed characters).

Example:

```
Symbols/
  8x16/
    _base_/
      41.glyph
      42.glyph
      30.glyph
      _MISSING.glyph
```

### 4.2 Overlay Layers

Any additional folder inside the pack is treated as an **overlay layer**.

Examples:

```
bold/
italic/
decorative/
emojis/
logos/
Asian/
Africa/
```

Properties of overlay layers:

- They may be **partial** (do not need to contain all glyphs).
- They override glyphs from `_base_` **only when a glyph file exists**.
- Missing glyphs automatically fall back to `_base_`.

---

## 5. Glyph Resolution Rules

When resolving a glyph for rendering (e.g., in a virtual terminal), the system follows this order:

1. Active overlay layer (e.g., `bold`, `Asian`, `logos`)
2. `_base_` layer
3. Fallback glyph (recommended: `_MISSING.glyph` or `3F.glyph`)

This ensures deterministic behavior and allows overlay layers to remain minimal.

---

## 6. File Naming Inside Layers

Inside any layer folder, glyph files follow the **Glyph Naming Standard**:

- Unicode glyphs: `<HEX>.glyph` (uppercase hex, no padding)
  - Example: `41.glyph` → U+0041 (`A`)
- Internal glyphs: `_<NAME>.glyph`
  - Example: `_AFW_LOGO_A.glyph`

The same glyph name may appear in multiple layers, representing different visual variants.

---

## 7. Cultural and Thematic Symbol Sets

The folder-based layering model intentionally supports cultural and thematic symbol sets without hierarchy or bias.

Examples:

```
Asian/
Africa/
Runic/
MathSymbols/
```

All such sets are treated as **peer overlays**. None of them replace `_base_`; they extend or override it selectively.

---

## 8. Tooling and UI Implications

Editors and tools (such as the Afrowave Glyph Editor) may:

- Display `_base_` as a protected or core layer
- Show coverage statistics per layer
- Visualize fallback paths
- Allow preview of multiple layers simultaneously

The directory structure is designed to make these features straightforward without additional metadata.

---

## 9. Design Goals Recap

This structure is designed to:

- Avoid confusion with traditional font systems
- Support pixel-perfect glyph editing
- Enable clean fallback behavior
- Allow unlimited extension without breaking compatibility
- Be simple enough for low-level systems like BOS and AfNAS-OS

---

_End of document_