# #218 cpu_relax: Don't use pause for MIPS under rev 2 [Merged]

> Username: neheb  
> Created at: 2019-11-13 01:13:01 UTC  
> Updated at: 2019-11-13 06:41:21 UTC  
> Merged at: 2019-11-13 06:27:00 UTC  
> Closed at: 2019-11-13 06:27:01 UTC  
> Url: https://github.com/boostorg/fiber/pull/218  

PAUSE was introduced in MIPS32r2.  
  
Signed-off-by: Rosen Penev <rosenp@gmail.com>  
  
Example of build failure: https://downloads.openwrt.org/snapshots/faillogs/mips_mips32/packages/boost/compile.txt  
  
Ctrl+F error: will show  
  
```  
{standard input}:1942: Error: opcode not supported on this processor: mips32 (mips32) `pause'  
{standard input}:2017: Error: opcode not supported on this processor: mips32 (mips32) `pause'  
```

---

## Comment 1

> Username: olk  
> Created_at: 2019-11-13 06:27:10 UTC  
> Url: https://github.com/boostorg/fiber/pull/218#issuecomment-553259357  

ty

---
