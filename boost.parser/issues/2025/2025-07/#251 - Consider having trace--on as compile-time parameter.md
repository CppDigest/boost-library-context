# #251 - Consider having trace::on as compile-time parameter [Closed]

> Username: andreasbuhr  
> Created at: 2025-07-26 18:42:01 UTC  
> Updated at: 2026-02-22 03:45:27 UTC  
> Closed at: 2026-02-22 03:45:27 UTC  
> Url: https://github.com/boostorg/parser/issues/251  

Because the trace-mode is a runtime parameter in bp::parse, but it is a compile-time parameter in most templates, the whole parser always exists twice in the generated binary. One time with trace mode enabled, one time with trace mode disabled. When tracing is not used, the generated binary is at least twice as big as necessary. The compilation time has parts which are worse-than-linear in the number of available parse_rule() definitions, so compile time is at least twice as long as necessary.  
  
If there was a define or a template parameter to globally disable the trace mode, I'd appreciate that very much.

---

## Comment 1

> Username: andreasbuhr  
> Created at: 2025-08-07 12:15:25 UTC  
> Url: https://github.com/boostorg/parser/issues/251#issuecomment-3163941389  

This is what I use: https://github.com/boostorg/parser/pull/267

---

## Comment 2

> Username: tzlaine  
> Created at: 2026-02-22 03:45:27 UTC  
> Url: https://github.com/boostorg/parser/issues/251#issuecomment-3940076857  

See PR #314.
