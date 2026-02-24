# #22 Disabled 'is_palindrome' with 'const char*' [Merged]

> Username: zamazan4ik  
> Created at: 2016-08-15 01:15:03 UTC  
> Updated at: 2016-08-17 01:19:16 UTC  
> Merged at: 2016-08-17 01:19:15 UTC  
> Closed at: 2016-08-17 01:19:15 UTC  
> Url: https://github.com/boostorg/algorithm/pull/22  

I think, this is the best way to solve problem with 'is_palindrome' and 'const char_' . If user will try to use const char_ with is_palindrome, user will get a compile error.  
  
Also i updated documentation.

---

## Comment 1

> Username: zamazan4ik  
> Created_at: 2016-08-16 02:17:08 UTC  
> Url: https://github.com/boostorg/algorithm/pull/22#issuecomment-239983764  

Now we don't disable C-string. I added support for it. Also is_palindrome supports nullptr, 0, NULL as null pointers.

---

## Comment 2

> Username: zamazan4ik  
> Created_at: 2016-08-16 13:24:24 UTC  
> Updated_at: 2016-08-16 13:24:51 UTC  
> Url: https://github.com/boostorg/algorithm/pull/22#issuecomment-240100022  

Be careful: you can't use 0, NULL and nullptr directly with 'is_palindrome'. You can use it as value of 'const char*'. It's a right behaviour, i think. Calling 'is_palindrome(nullptr)', 'is_palindrome(0)', 'is_palindrome(NULL)' is silly. There are not any useful cases for it.

---
