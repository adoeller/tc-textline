# Text Line

A **content (WDX) plugin for [Total Commander](https://www.ghisler.com/)** that
exposes individual lines of a text file as content fields — usable in custom
columns, the search dialog, tooltips, and multi-rename.

- Original by Alexey Fomin (`http://ledsoft.narod.ru`)
- Lazarus/FPC port + extensions (32/64-bit, last-line & line-count fields,
  automatic Unicode detection, `SkipEmpty`)

---

## Fields

| Field         | Meaning                                   | Type    |
|---------------|-------------------------------------------|---------|
| `1` … `10`    | Line 1 to 10, counted from the top        | text    |
| `-3`          | Third line from the end                   | text    |
| `-2`          | Second line from the end                  | text    |
| `-1`          | Last line of the file                     | text    |
| `Line count`  | Total number of lines                     | numeric |

The text fields expose two **units**:

| Unit  | Meaning                                                      |
|-------|--------------------------------------------------------------|
| `win` | Interpret legacy single-byte text as the system **ANSI** code page |
| `dos` | Interpret legacy single-byte text as the system **OEM (DOS)** code page |

For Unicode files both units return the same correct text.

---

## Encoding

The encoding of each file is **detected automatically**:

- UTF-8 — with or without BOM
- UTF-16 little-endian and big-endian — with or without BOM
- otherwise legacy single-byte text (ANSI / OEM, selected by the unit)

Non-ASCII characters are returned correctly as Unicode.

---

## Configuration

Settings live in **`TextLine.ini`**, next to the plugin
(`TextLine.example.ini` is a documented template). Without a settings file the
plugin handles **all files** and replaces nothing.

```ini
[Options]
; Space-separated list of handled extensions; empty = all files.
Extensions=txt ini inf

; Skip empty (whitespace-only) lines.
SkipEmpty=0

[Replaces]
; Rules of the form  S<n>=<search>=<replacement>  (S1..S20).
; Remove "***":          S1=***=
; Replace "test"->"demo": S2=test=demo
S1=
```

### `SkipEmpty`

When set to `1`, empty (whitespace-only) lines are ignored:

- `1` … `10` return the first 10 **non-empty** lines from the top,
- `-3` / `-2` / `-1` return the last non-empty lines from the bottom,
- `Line count` counts only non-empty lines.

---

## Performance

- Each file is read **once** and cached (keyed by name, size and timestamp), so
  all fields of the same file are served from memory.
- Large files are **not** read in full: only the head and tail are scanned for
  the line fields.
- `Line count` streams the file once — and only when the field is actually used.

---

## Building

Open **`Src/TextLine.lpi`** in [Lazarus](https://www.lazarus-ide.org/) and build:

| Build mode | Output           | Total Commander |
|------------|------------------|-----------------|
| `Win32`    | `TextLine.wdx`   | 32-bit          |
| `Win64`    | `TextLine.wdx64` | 64-bit          |

Copy the resulting `.wdx` / `.wdx64` (and `TextLine.ini`) into a plugin folder
and install it via Total Commander's configuration
(*Configuration → Options → Plugins → Content plugins*).

---

## Project layout

```
Src/
  TextLine.lpr     library main unit (FPC/Lazarus)
  TextLine.lpi     Lazarus project (Win32 + Win64 build modes)
  ContPlug.pas     Total Commander content-plugin interface
  SProc.pas        small string / INI helpers
TextLine.example.ini
README.md
```

---

## License

As is, no warranty — freeware. Source included.

## Thanks

- Christian Ghisler — for Total Commander
- Alexey Torgashin — for the File Descriptions source
