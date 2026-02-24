# #171 Applied clang-format and stripped all trailing whitespace. [Merged]

> Username: anadon  
> Created at: 2019-05-02 20:32:53 UTC  
> Updated at: 2019-08-26 17:56:27 UTC  
> Merged at: 2019-08-26 17:56:27 UTC  
> Closed at: 2019-08-26 17:56:27 UTC  
> Url: https://github.com/boostorg/graph/pull/171  

The biggest of merges for the smallest of function changes.

---

## Comment 1

> Username: anadon  
> Created_at: 2019-05-02 20:50:48 UTC  
> Url: https://github.com/boostorg/graph/pull/171#issuecomment-488827586  

@jzmaddock Up for a less frustrating review when you're ready.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2019-05-03 08:21:59 UTC  
> Url: https://github.com/boostorg/graph/pull/171#issuecomment-489003305  

Is there a .clang-format file with this?

---

## Comment 3

> Username: anadon  
> Created_at: 2019-05-03 16:53:24 UTC  
> Url: https://github.com/boostorg/graph/pull/171#issuecomment-489165025  

There is one pre-existing in the repo.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2019-05-03 18:57:44 UTC  
> Url: https://github.com/boostorg/graph/pull/171#issuecomment-489204090  

Apologies, I missed the .clang-format file :(  
  
I notice that all C++03 tests are now failing due to "> >" changing to ">>", can this be fixed in the .clang-format file and the files formatted afresh?  
  
Thanks!  John.

---

## Comment 5

> Username: anadon  
> Created_at: 2019-05-03 19:26:54 UTC  
> Url: https://github.com/boostorg/graph/pull/171#issuecomment-489212462  

It certainly can.  I tested on my system, and all tests passed so I didn't notice that.

---

## Comment 6

> Username: anadon  
> Created_at: 2019-05-06 13:30:31 UTC  
> Url: https://github.com/boostorg/graph/pull/171#issuecomment-489620907  

@jzmaddock All is green.

---
