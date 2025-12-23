# Next.js 16.1.x MDX Multibyte Character Bug Reproduction

This repo demonstrates a regression in Next.js 16.1.0+ where MDX files containing multibyte characters (e.g., Japanese full-width parentheses) cause SWC to panic during build.

## Bug Description

When building an MDX file with multibyte characters like Japanese full-width parentheses `（` `）`, SWC crashes with:

```
thread '<unnamed>' panicked at swc_common-18.0.0/src/source_map.rs:657:62:
byte index 88 is not a char boundary; it is inside '（' (bytes 87..90)
```

The issue is **not** related to `export const metadata` content, but to any multibyte character in the MDX file body.

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
