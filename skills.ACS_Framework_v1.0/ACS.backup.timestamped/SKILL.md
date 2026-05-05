---
name: ACS.backup.timestamped
description: Use when adding or auditing JSON export functionality in any STRATOS-style app. Ensures successive backups never overwrite each other by stamping filenames with YYYYMMDD_HHMM. Pattern was specified in REQ01 (Versión 6.txt) and is now the project default.
license: CC-BY-4.0
---

# Timestamped JSON backups

Every export — global backup and per-entity — must carry a timestamp so successive saves don't clobber each other.

## Helper

```js
const tsTag = (d = new Date()) => {
    const p = n => String(n).padStart(2,'0');
    return `${d.getFullYear()}${p(d.getMonth()+1)}${p(d.getDate())}_${p(d.getHours())}${p(d.getMinutes())}`;
};
const tsName = (base, ext='json') => `${base}_${tsTag()}.${ext}`;
```

## Apply at all export call sites

```js
// Global backup
exportJSON({...}, tsName('stratos_backup'));
// → stratos_backup_20260504_2255.json

// Per-entity export — store ENTITIES.file as the base (no extension)
exportJSON(ent.data, tsName(ent.file));
// → stratos_coas_20260504_2255.json
```

## Why minute-precision (not second)

Operators rarely need sub-minute granularity, and minute resolution keeps filenames sortable in a directory listing without timestamp clutter. If you need sub-minute, switch `tsTag` to include seconds — but then update the docs.

## Rule for `_meta` block in global backup

Always include in the export payload:
```js
_meta: { app:'STRATOS', version:'v006', exportedAt:new Date().toISOString() }
```
This survives round-trip and helps diagnose backups by hand.

## Validation

Applied to all `SettingsView` exports in v006. Confirmed file naming pattern matches the user-supplied sample `stratos_backup_20260504_225.json`.
