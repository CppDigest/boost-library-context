# #233 De-UTF8 the directory name [Closed]

> Username: tjcw  
> Created at: 2024-11-05 14:04:54 UTC  
> Updated at: 2024-11-12 14:45:16 UTC  
> Closed at: 2024-11-12 14:45:16 UTC  
> Url: https://github.com/boostorg/wave/pull/233  

AIX does not support UTF8 characters in directory and file names. One of the files in the tests is named like this, and this causes problems unpacking BOOST on AIX.  
  
This PR renames the directory, and adds a stage in bootstrap.sh/bootstrap.cmd to fix up the name so that non-AIX platforms can continue to run their tests.

---

## Comment 1

> Username: hkaiser  
> Created_at: 2024-11-05 14:09:58 UTC  
> Url: https://github.com/boostorg/wave/pull/233#issuecomment-2457279802  

> This PR renames the directory, and adds a stage in bootstrap.sh/bootstrap.cmd to fix up the name so that non-AIX platforms can continue to run their tests.  
  
Could you link this change here, please?

---

## Comment 2

> Username: tjcw  
> Created_at: 2024-11-05 14:22:31 UTC  
> Url: https://github.com/boostorg/wave/pull/233#issuecomment-2457310231  

https://github.com/boostorg/boost/pull/978 is the PR that changes bootstrap.sh and bootstrap.cmd

---

## Comment 3

> Username: tjcw  
> Created_at: 2024-11-05 16:15:36 UTC  
> Url: https://github.com/boostorg/wave/pull/233#issuecomment-2457608131  

https://github.com/bfgroup/b2/pull/421 is the PR in the reposistory for `b2` changes. Please refer to this rather than to boostorg/boost#978 .

---

## Comment 4

> Username: tjcw  
> Created_at: 2024-11-12 14:32:20 UTC  
> Url: https://github.com/boostorg/wave/pull/233#issuecomment-2470695365  

After rechecking, it looks like AIX does not need this patch. Closing.

---

## Comment 5

> Username: tjcw  
> Created_at: 2024-11-12 14:45:16 UTC  
> Url: https://github.com/boostorg/wave/pull/233#issuecomment-2470728843  

Closing as above

---
