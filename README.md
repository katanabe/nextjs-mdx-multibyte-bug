# Next.js 16.1.x MDX Multibyte Character Bug Reproduction

This repo demonstrates a regression in Next.js 16.1.0+ where MDX files containing Japanese (multibyte) characters cause SWC to panic during build.

## Bug Description

When building an MDX file with Japanese full-width parentheses `（` `）`, SWC crashes with:

```
thread '<unnamed>' panicked at swc_common-18.0.0/src/source_map.rs:657:62:
byte index 93 is not a char boundary; it is inside '（' (bytes 92..95)
```

## Reproduction Steps

1. `npm install`
2. `npm run build`

## Version Comparison

| Version | Result |
|---------|--------|
| 16.0.10 | ✅ Build succeeds |
| 16.1.0  | ❌ SWC panic |
| 16.1.1  | ❌ SWC panic |

## Root Cause

Likely related to SWC version bump in 16.1.0:
- #86240
- #86689

The issue is that Rust string slicing attempts to access a byte index in the middle of a multibyte UTF-8 character.
