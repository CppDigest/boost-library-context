# #282 fix buffered_channel of issue #256 - bug in spinlock_ttas [Merged]

> Username: shredingu  
> Created at: 2021-09-03 10:27:18 UTC  
> Updated at: 2023-08-18 05:24:09 UTC  
> Merged at: 2023-08-18 05:24:03 UTC  
> Closed at: 2023-08-18 05:24:03 UTC  
> Url: https://github.com/boostorg/fiber/pull/282  

I also experienced this problem #256 and was able to reproduce the bug with @dixlorenz 's snippet. It seems the @dixlorenz suggestion of yielding inside spinlock_ttas would fix this but the problem is context header is only defined later. Since I don't have a deep understanding of this project I didn't felt comfortable of changing include header files order, also have no clue if that would be possible.   
  
I managed to fix the @dixlorenz snippet by yielding upfront in every buffered_channel call if spinlock_ttas try_lock fails. Of course this doesn't fix the spinlock_ttas problem but at least it seems to fix the buffered_channel hanging during the spinlock.

---

## Comment 1

> Username: olk  
> Created_at: 2023-08-18 05:24:09 UTC  
> Url: https://github.com/boostorg/fiber/pull/282#issuecomment-1683369936  

ty

---
