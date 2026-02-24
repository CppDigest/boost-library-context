# #332 Moves logging into reader_fsm [Merged]

> Username: anarthal  
> Created at: 2025-10-15 12:07:11 UTC  
> Updated at: 2025-10-15 15:36:58 UTC  
> Merged at: 2025-10-15 15:36:54 UTC  
> Closed at: 2025-10-15 15:36:55 UTC  
> Url: https://github.com/boostorg/redis/pull/332  

* Removes logging all the reader actions, and logs specific messages inside the reader_fsm instead  
* Adds constructors to reader actions  
* Makes reader_fsm use connection_state  
* Refactors reader_fsm tests  
* Moves exec_fsm action printing to test code

---

## Comment 1

> Username: anarthal  
> Created_at: 2025-10-15 12:08:22 UTC  
> Url: https://github.com/boostorg/redis/pull/332#issuecomment-3406083466  

This should entail no functional change (aside from logging). I'd find it useful to have this if I need to somehow touch the reader in #328

---
