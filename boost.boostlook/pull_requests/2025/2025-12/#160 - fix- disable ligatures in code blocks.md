# #160 fix: disable ligatures in code blocks [Merged]

> Username: julioest  
> Created at: 2025-12-11 22:55:20 UTC  
> Updated at: 2025-12-11 22:55:35 UTC  
> Merged at: 2025-12-11 22:55:35 UTC  
> Closed at: 2025-12-11 22:55:35 UTC  
> Url: https://github.com/boostorg/boostlook/pull/160  

## Summary  
- Disables ligatures (`liga`) and contextual alternates (`calt`) in code blocks  
- Shows literal characters instead of combined glyphs for better code readability  
  
## Changes  
- `font-feature-settings`: Set `calt` and `liga` to `0` instead of `1`

---
