# #137 use ZSTD_MAX_CLEVEL and ZSTD_CLEVEL_DEFAULT for compression level [Open]

> Username: okravets  
> Created at: 2021-10-29 16:28:10 UTC  
> Updated at: 2022-02-01 15:36:20 UTC  
> Url: https://github.com/boostorg/iostreams/pull/137  

use ZSTD_MAX_CLEVEL and ZSTD_CLEVEL_DEFAULT macro for compression levels

---

## Comment 1

> Username: rdoeffinger  
> Created_at: 2022-02-01 15:36:20 UTC  
> Url: https://github.com/boostorg/iostreams/pull/137#issuecomment-1026978723  

I don't think this can work, there seems to be no ZSTD_MAX_CLEVEL in any of the zstd.h headers I can find (e.g. in Ubuntu or in the zstd project).  
Instead there is the ZSTD_maxCLevel function call, however that cannot be used in this context.  
Even the ZSTD_CLEVEL_DEFAULT should be checked since old versions did not have that either.  
Also does it really matter? 19 is much slower than most will find acceptable anyway, and it is compatible with most versions.  
I don't think it prevents anyone from using 22 manually (or even calling ZSTD_maxCLevel) if really keen on it.  
It seems to me the current code is a reasonable abstraction.

---
