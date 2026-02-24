# #118 - [review] describe library as C++11, add mentions for later standards where applicable [Closed]

> Username: badair  
> Created at: 2017-04-09 03:47:28 UTC  
> Updated at: 2017-07-07 01:13:38 UTC  
> Closed at: 2017-07-02 20:37:39 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/118  

from http://lists.boost.org/Archives/boost/2017/04/234234.php

---

## Comment 1

> Username: ldionne  
> Created at: 2017-07-05 17:38:29 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/118#issuecomment-313174076  

Awesome job, I love the matrix.

---

## Comment 2

> Username: ldionne  
> Created at: 2017-07-05 17:40:01 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/118#issuecomment-313174492  

You may want to cover future compiler versions where everything works by saying something like "Clang >= 4.0.0". Otherwise you'll have to revise these tables every time a new supported compiler is released, which may be a lot of work (and will end up with very large matrices).

---

## Comment 3

> Username: badair  
> Created at: 2017-07-07 01:13:10 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/118#issuecomment-313560661  

@ldionne I considered this, but I think this would be misleading. I am only comfortable listing compilers that I have tested. While I have made every effort to future-proof the code using feature detection macros, compiler regressions are possible.

---

## Comment 4

> Username: ldionne  
> Created at: 2017-07-07 01:13:38 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/118#issuecomment-313560715  

Fair enough!
