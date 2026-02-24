# #97 - best_compression for zst filter should be increased from 19 to 22 [Open]

> Username: KBentley57  
> Created at: 2019-06-05 19:43:45 UTC  
> Updated at: 2022-02-01 15:37:06 UTC  
> Url: https://github.com/boostorg/iostreams/issues/97  

zstd compression allows up to 22, instead of 19.  Tested to be working when changed manually.  The maximum compression level was changed from 19 in zstd version 0.8.1 ( https://github.com/facebook/zstd/commit/be6180c1422f6272f56a3b0264073432f3ec0fce ) in 2016, which practically nobody will be using at this point.

---

## Comment 1

> Username: okravets  
> Created at: 2021-10-29 16:34:40 UTC  
> Url: https://github.com/boostorg/iostreams/issues/97#issuecomment-954885639  

Created https://github.com/boostorg/iostreams/pull/137 for this

---

## Comment 2

> Username: rdoeffinger  
> Created at: 2022-02-01 15:37:06 UTC  
> Url: https://github.com/boostorg/iostreams/issues/97#issuecomment-1026979534  

Commented on the pull request. Not sure there's really enough value in updating this to bother?
