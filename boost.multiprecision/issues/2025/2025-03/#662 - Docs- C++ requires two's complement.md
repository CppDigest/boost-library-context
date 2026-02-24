# #662 - Docs: C++ requires two's complement [Closed]

> Username: akrzemi1  
> Created at: 2025-03-10 22:53:06 UTC  
> Updated at: 2025-04-08 10:41:49 UTC  
> Closed at: 2025-04-08 10:41:49 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/662  

The Multiprecision docs  at  
https://www.boost.org/doc/libs/1_87_0/libs/multiprecision/doc/html/boost_multiprecision/tut/ints/cpp_int.html  
  
say that "there's no requirement for fundamental_types to be 2's complement", which becomes incorrect since C++20, which in fact requires the two's complement representation for built-in integers: https://eel.is/c++draft/basic.fundamental#3.  
  
There's also a small typo in the vicinity:  
"In might be tempting" -> "*It* might be tempting"

---

## Comment 1

> Username: ramonGomezEnc  
> Created at: 2025-03-11 02:24:23 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/662#issuecomment-2712336293  

hello, very interested in this issue, can I be assigned to solve it?

---

## Comment 2

> Username: ckormanyos  
> Created at: 2025-04-07 10:49:02 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/662#issuecomment-2782901802  

> say that "there's no requirement for fundamental_types to be 2's complement", which becomes incorrect since C++20  
  
So this issue will be a document update rather than needing any code changes, right?  
  
Cc: @akrzemi1

---

## Comment 3

> Username: akrzemi1  
> Created at: 2025-04-07 10:54:53 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/662#issuecomment-2782916386  

Correct. Docs changes only.

---

## Comment 4

> Username: ckormanyos  
> Created at: 2025-04-08 07:36:57 UTC  
> Updated at: 2025-04-08 07:53:12 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/662#issuecomment-2785520000  

There are a few typos including "where as", maybe should be whereas, the one you mention also. I can catch these.  
  
How about the new sentence:  
  
- "It should be noted that there is a requirement for fundamental types to be 2's complement as of C++20, so this is a slight difference regarding internal representation."  
  
Cc: @jzmaddock and @akrzemi1

---

## Comment 5

> Username: ckormanyos  
> Created at: 2025-04-08 07:59:54 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/662#issuecomment-2785575884  

See #672
