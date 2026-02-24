# #56 - unused parameter warning when compiling with assertions disabled [Closed]

> Username: viboes  
> Created at: 2017-09-17 21:32:05 UTC  
> Updated at: 2017-10-14 17:12:17 UTC  
> Closed at: 2017-10-14 10:10:39 UTC  
> Url: https://github.com/boostorg/container/issues/56  

There are some functions here that would need to mask this warning  
  
https://github.com/boostorg/container/blob/6a9e46ed6e5e8b1e24c5d9be7b2d6e52c9485fe0/include/boost/container/throw_exception.hpp#L60  
  
Please could you silent these warnings?

---

## Comment 1

> Username: igaztanaga  
> Created at: 2017-09-18 21:13:26 UTC  
> Url: https://github.com/boostorg/container/issues/56#issuecomment-330358092  

Ok, can you please tell me what the warning says, with which compiler?

---

## Comment 2

> Username: viboes  
> Created at: 2017-09-18 23:53:18 UTC  
> Url: https://github.com/boostorg/container/issues/56#issuecomment-330388254  

I got it at work with a TI.  
  
When  `BOOST_ASSERT_MSG(!"boost::container out_of_range thrown", str); `is nothing, the variable `str` is not used.  
  
I can try to get the exact message and the exact compiler version, but I don't think this is specific to this compiler.

---

## Comment 3

> Username: igaztanaga  
> Created at: 2017-09-30 22:55:53 UTC  
> Url: https://github.com/boostorg/container/issues/56#issuecomment-333341294  

Let's see if commit:  
  
https://github.com/boostorg/container/commit/520dd7cbddfa26245edea719d52161e14e41fa95  
  
fixes the problem.

---

## Comment 4

> Username: igaztanaga  
> Created at: 2017-10-01 08:46:52 UTC  
> Url: https://github.com/boostorg/container/issues/56#issuecomment-333362413  

Trying to refine it further in commit:  
  
https://github.com/boostorg/container/commit/407aabd77bd5e1080394e5519b1061f27f2b7130

---

## Comment 5

> Username: igaztanaga  
> Created at: 2017-10-14 10:10:39 UTC  
> Url: https://github.com/boostorg/container/issues/56#issuecomment-336625162  

Closing it as fixed.

---

## Comment 6

> Username: viboes  
> Created at: 2017-10-14 17:12:17 UTC  
> Url: https://github.com/boostorg/container/issues/56#issuecomment-336649513  

Gracias
