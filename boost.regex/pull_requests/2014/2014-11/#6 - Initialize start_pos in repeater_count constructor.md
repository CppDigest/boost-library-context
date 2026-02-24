# #6 Initialize start_pos in repeater_count constructor [Closed]

> Username: tempoz  
> Created at: 2014-11-11 16:11:27 UTC  
> Updated at: 2014-11-13 11:04:20 UTC  
> Closed at: 2014-11-13 11:04:20 UTC  
> Url: https://github.com/boostorg/regex/pull/6  

start_pos is otherwise uninitialized leaving this constructor. Uncovered by Coverity, this issue has CID10549.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2014-11-13 11:04:20 UTC  
> Url: https://github.com/boostorg/regex/pull/6#issuecomment-62875017  

See https://github.com/boostorg/regex/commit/dbda6314da08f8b63c668fa436f6715d55b317c3

---
