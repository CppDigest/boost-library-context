# #47 disable .set mips2 for mips release r6 [Merged]

> Username: wzssyqa  
> Created at: 2017-12-31 06:17:04 UTC  
> Updated at: 2018-01-03 05:19:00 UTC  
> Merged at: 2018-01-03 05:19:00 UTC  
> Closed at: 2018-01-03 05:19:00 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/47  

MIPS r6 changed the encoding for `ll' instruction,  
if we `.set mips2', it will generate the old encoding for `ll'.  
  
So here we disable it for r6.

---

## Comment 1

> Username: pdimov  
> Created_at: 2018-01-03 01:20:36 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/47#issuecomment-354918706  

I suspect that we also need to disable that `.set mips2` for MIPS64 which implies at least 3; but since I know next to nothing about MIPS, I can't be sure. :-)

---

## Comment 2

> Username: wzssyqa  
> Created_at: 2018-01-03 01:43:00 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/47#issuecomment-354921266  

here, we use only mips32 instructions, all of them are included in mips64,  
so set mips2 here won't make some trouble for us.

---
