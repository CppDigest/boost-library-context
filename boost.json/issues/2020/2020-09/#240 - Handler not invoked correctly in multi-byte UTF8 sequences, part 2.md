# #240 - Handler not invoked correctly in multi-byte UTF8 sequences, part 2 [Closed]

> Username: rtobar  
> Created at: 2020-09-01 03:36:29 UTC  
> Updated at: 2020-09-02 01:36:10 UTC  
> Closed at: 2020-09-01 23:38:12 UTC  
> Url: https://github.com/boostorg/json/issues/240  

This was fixed for writes of size 1 in #162, but apparently not for the general case, see https://github.com/rtobar/json/commit/6d766ef6643429296e6368513849ff1f49384c4f for a test reproducing further errors plus a new example string that breaks.

---

## Comment 1

> Username: sdkrystian  
> Created at: 2020-09-01 20:58:28 UTC  
> Url: https://github.com/boostorg/json/issues/240#issuecomment-685128750  

*sigh* I forgot to flush characters preceding UTF-8 byte sequences. My bad.

---

## Comment 2

> Username: rtobar  
> Created at: 2020-09-02 01:36:10 UTC  
> Url: https://github.com/boostorg/json/issues/240#issuecomment-685229046  

Looks good, thanks!
