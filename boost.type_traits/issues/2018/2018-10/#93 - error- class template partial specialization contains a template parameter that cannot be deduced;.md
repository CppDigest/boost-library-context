# #93 - error: class template partial specialization contains a template parameter that cannot be deduced; [Closed]

> Username: viboes  
> Created at: 2018-10-11 05:25:30 UTC  
> Updated at: 2019-01-08 09:09:30 UTC  
> Closed at: 2019-01-08 09:09:30 UTC  
> Url: https://github.com/boostorg/type_traits/issues/93  

```  
error: class template partial specialization contains a template parameter that cannot be deduced; this partial specialization will never be used [-Wunusable-partial-specialization]  
   struct is_function<Ret BOOST_TT_DEF_CALL(Args...)BOOST_TT_NOEXCEPT_DECL> : public true_type {};  
          ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
```  
  
See https://circleci.com/gh/boostorg/thread/520

---

## Comment 1

> Username: pdimov  
> Created at: 2019-01-05 18:21:53 UTC  
> Url: https://github.com/boostorg/type_traits/issues/93#issuecomment-451678926  

This continues to be an issue on f.ex. xcode9.1: https://travis-ci.org/boostorg/fiber/jobs/475698007  
  
I don't know why the Type Traits tests don't catch it (apart from not having xcode9.1) - the error is probably issued by Clang 6 or 7, whereas the Travis matrix only goes to 5 at the moment.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2019-01-06 10:35:26 UTC  
> Url: https://github.com/boostorg/type_traits/issues/93#issuecomment-451731339  

This passes with 9.4 (https://travis-ci.org/boostorg/type_traits/builds/475930539?utm_source=github_status&utm_medium=notification), will add a 9.1 test as well and we'll see.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2019-01-08 09:08:28 UTC  
> Url: https://github.com/boostorg/type_traits/issues/93#issuecomment-452225345  

Fixed in develop.
