# #19 Sync queue fixes [Closed]

> Username: chrismanning  
> Created at: 2014-07-05 00:37:48 UTC  
> Updated at: 2014-07-06 14:46:16 UTC  
> Closed at: 2014-07-06 13:26:13 UTC  
> Url: https://github.com/boostorg/thread/pull/19  

These changes fix a couple of things in the `sync*_queue` classes:  
- `wait_*` member functions didn't wait.  
- relevant single and multi-threaded tests added.  
- multi-threaded tests were simplified a little and now only test the non-deprecated functions.  
- `close()` was called in several places on throw when it should have the strong guarantee.  
- `move_if_noexcept` is needed for proper exception safety guarantees. Waiting on https://github.com/boostorg/move/pull/1 and then a few `/*_if_noexcept*/` simply need uncommenting.

---

## Comment 1

> Username: viboes  
> Created_at: 2014-07-06 09:16:01 UTC  
> Url: https://github.com/boostorg/thread/pull/19#issuecomment-48106772  

Please, let me know if I have taken in account the changes you proposed , even if I have not merged your PR, so that we can close this PR.  
  
It was not my intention to discredit your PR.   
I'll be more careful next time.

---

## Comment 2

> Username: chrismanning  
> Created_at: 2014-07-06 13:26:13 UTC  
> Url: https://github.com/boostorg/thread/pull/19#issuecomment-48111752  

Thank you. Yes I'll close it now. Keep in mind the forthcoming move_if_noexcept addition to Boost.Move which can be used so that the exception safety guaranties are valid and the data structure is not invalidated when a move constructor throws.

---

## Comment 3

> Username: viboes  
> Created_at: 2014-07-06 14:46:16 UTC  
> Url: https://github.com/boostorg/thread/pull/19#issuecomment-48113529  

Yes I will take care of this as soon as move_if_noexcept is available.

---
