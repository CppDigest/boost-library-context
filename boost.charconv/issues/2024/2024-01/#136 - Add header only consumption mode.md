# #136 - Add header only consumption mode [Closed]

> Username: mborland  
> Created at: 2024-01-24 07:01:45 UTC  
> Updated at: 2024-01-29 10:49:44 UTC  
> Closed at: 2024-01-29 10:49:44 UTC  
> Url: https://github.com/boostorg/charconv/issues/136  

See: https://github.com/boostorg/json#header-only

---

## Comment 1

> Username: pdimov  
> Created at: 2024-01-24 14:31:33 UTC  
> Url: https://github.com/boostorg/charconv/issues/136#issuecomment-1908248560  

This isn't a particularly good or useful idea. It's not practical to have a header-only _option_ for this library. Either it should be always header-only, or always compiled.  
  
That's because now every downstream consumer needs to pick a mode, and they all must pick the same mode. E.g. if JSON uses the library in header-only mode, and my code uses JSON, my code (and all its dependencies) also must use header-only mode. And conversely, if JSON uses compiled Charconv, everyone else must, too.  
  
Also, the above link doesn't actually describe header-only, even though it's called such.
