# #16 Prevent unnecessary string scanning. [Closed]

> Username: EdSchouten  
> Created at: 2015-08-27 13:42:27 UTC  
> Updated at: 2018-07-18 17:59:59 UTC  
> Closed at: 2018-07-18 17:59:59 UTC  
> Url: https://github.com/boostorg/regex/pull/16  

The internal strcat_s() function already calls strlen() on the  
source/destination strings to check for overflows. Instead of calling  
into strcat() and scanning over the string again, we can simply call  
memcpy() to copy the text over to the right place.  
  
Apply the same optimization to strcpy_s(). memcpy() is almost always  
faster than strcpy(), for the reason that testing against a counter is  
faster than searching for a null byte in the input sequence.  
  
This change allows the code to build with Nuxi CloudABI's C library[1].  
This C library does not provide implementations for strcpy(), strcat()  
and strncat(), as in addition to being potentially insecure, the  
observation is that if you want to use these securely, you can almost  
always use memcpy() instead.  
  
[1] cloudlibc: https://github.com/NuxiNL/cloudlibc

---

## Comment 1

> Username: Lastique  
> Created_at: 2015-08-27 14:58:00 UTC  
> Updated_at: 2015-08-27 15:20:33 UTC  
> Url: https://github.com/boostorg/regex/pull/16#discussion_r38104983  

lenSource and lenDestination names are confusingly similar while one includes the terminating zero and the other doesn't.

---

## Comment 2

> Username: EdSchouten  
> Created_at: 2015-08-27 15:01:57 UTC  
> Updated_at: 2015-08-27 15:20:33 UTC  
> Url: https://github.com/boostorg/regex/pull/16#discussion_r38105494  

There two ways we can resolve this. Both ways are fine by me. Be sure to let me know which one you prefer.  
- Rename `lenSource` to something like `lenSourceWithNull`, or  
- Don't add the `+ 1` and add it in both places where we need it.  
  
My only concern with the second approach is that it makes the arithmetic that's going on less obvious. Right now it is quite obvious that this will never access the buffer out of bounds.

---

## Comment 3

> Username: Lastique  
> Created_at: 2015-08-27 15:15:44 UTC  
> Updated_at: 2015-08-27 15:20:33 UTC  
> Url: https://github.com/boostorg/regex/pull/16#discussion_r38107255  

I think renaming would be better.

---

## Comment 4

> Username: EdSchouten  
> Created_at: 2015-08-27 15:20:56 UTC  
> Url: https://github.com/boostorg/regex/pull/16#discussion_r38107910  

Done!

---

## Comment 5

> Username: jeking3  
> Created_at: 2018-07-17 11:33:09 UTC  
> Url: https://github.com/boostorg/regex/pull/16#issuecomment-405550874  

What happened to this PR?  There was immediate engagement then it was dropped for 3 years?  
  
This PR targets the wrong destination branch.  
The author has since deleted his branch.  
The code changes conflict with current master (maybe some of the changes were made already?)  
It looks like two different fixes, one for accessing methods and one to optimize the copy.  
  
Recommend splitting and resolving, or closing the PR.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2018-07-18 17:59:59 UTC  
> Url: https://github.com/boostorg/regex/pull/16#issuecomment-406021018  

Manually merged to develop.

---
