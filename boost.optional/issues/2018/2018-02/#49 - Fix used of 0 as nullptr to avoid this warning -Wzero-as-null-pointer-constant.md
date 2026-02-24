# #49 - Fix used of 0 as nullptr to avoid this warning -Wzero-as-null-pointer-constant [Closed]

> Username: viboes  
> Created at: 2018-02-11 10:18:00 UTC  
> Updated at: 2018-03-01 01:07:48 UTC  
> Closed at: 2018-03-01 01:07:48 UTC  
> Url: https://github.com/boostorg/optional/issues/49  

Hi,   
I'm compiling with -Werror,-Wzero-as-null-pointer-constant in clang-5.0.0 and getting  
  
```  
/Users/viboes/github/modular-boost3/boost/optional/detail/optional_reference_spec.hpp:139:172: error: zero as null pointer constant [-Werror,-Wzero-as-null-pointer-constant]  
      optional(U& rhs, BOOST_DEDUCED_TYPENAME boost::enable_if_c<detail::is_same_decayed<T, U>::value && !detail::is_const_integral_bad_for_conversion<U>::value>::type* = 0) BOOST_NOEXCEPT  
                                                                                                                                                                           ^  
                                                                                                                                                                           nullptr  
  
```  
Please, could you fix it.  
Vicente

---

## Comment 1

> Username: viboes  
> Created at: 2018-02-11 11:12:08 UTC  
> Url: https://github.com/boostorg/optional/issues/49#issuecomment-364743338  

Hrr, I forgot that Boost.Optional should work for C++03, so using `nullptr` there will not help.  
  
Wondering if we need a BOOST_NULLPTR or a BOOST_NULLPTR_0 that solves the issue conditionally.  
  
What do you think of something more elaborated as https://wandbox.org/permlink/auOe0MGi0cGLHla9 ? Do you see some liabilities other than the expressed via conditional compilation?

---

## Comment 2

> Username: akrzemi1  
> Created at: 2018-02-12 07:43:26 UTC  
> Url: https://github.com/boostorg/optional/issues/49#issuecomment-364847881  

I'll fix that. It is only used with `enable_if`s, so I can just change the pointers to type `bool`.

---

## Comment 3

> Username: viboes  
> Created at: 2018-02-12 19:25:57 UTC  
> Url: https://github.com/boostorg/optional/issues/49#issuecomment-365033993  

Yes, this is a better solution.

---

## Comment 4

> Username: akrzemi1  
> Created at: 2018-02-15 21:54:02 UTC  
> Url: https://github.com/boostorg/optional/issues/49#issuecomment-366074106  

The warnings should be gone now in branch `develop`

---

## Comment 5

> Username: viboes  
> Created at: 2018-02-16 07:11:01 UTC  
> Url: https://github.com/boostorg/optional/issues/49#issuecomment-366160294  

Thanks.  
  
Please, close it as soon as it is merged to master.

---

## Comment 6

> Username: akrzemi1  
> Created at: 2018-03-01 01:07:48 UTC  
> Url: https://github.com/boostorg/optional/issues/49#issuecomment-369439624  

Merged.
