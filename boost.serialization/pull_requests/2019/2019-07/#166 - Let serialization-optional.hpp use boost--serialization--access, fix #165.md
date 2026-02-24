# #166 Let serialization/optional.hpp use boost::serialization::access, fix #165 [Closed]

> Username: cosurgi  
> Created at: 2019-07-24 16:57:33 UTC  
> Updated at: 2019-08-01 10:19:59 UTC  
> Closed at: 2019-08-01 10:19:59 UTC  
> Url: https://github.com/boostorg/serialization/pull/166  

This merge request fixes #165 , the original code is retained. To enable new behavior use `#define BOOST_OPTIONAL_CONSTRUCTOR_ACCESS`.  
  
Unfortunately I am not sure how to check for existence of private default constructor, so I left this check empty.

---

## Comment 1

> Username: cosurgi  
> Created_at: 2019-07-25 12:37:36 UTC  
> Updated_at: 2019-07-25 12:45:39 UTC  
> Url: https://github.com/boostorg/serialization/pull/166#issuecomment-515026419  

@robertramey I managed to detect if there is a not public default constructor. So now we have old (fast) behavior if there is a public constructor and the workaround behavior if the constructor is private. So no loss in performance.  
  
Now the only problem is that it works only in C++17. We need to find a way to make it work on older compilers.  
  
I know it's still a long way to go. But we are closer to removing this regression and restoring old behavior.

---

## Comment 2

> Username: cosurgi  
> Created_at: 2019-08-01 10:19:58 UTC  
> Url: https://github.com/boostorg/serialization/pull/166#issuecomment-517223361  

I have a different idea to get back to the "old" serialization::load pre-1.65 code and adjust it to rely on access::construct (via stack_construct, like other serializable types) before "ar >> ....", thus fixing the problem of serializing into un-initialized object and also fixing my problem. My classes rely on "friend class boost::serialization::access" to ensure that only serialization code is able to use the default constructor and I really want to stick to this design decision, since it prevents bugs where objects might be otherwise constructed with no parameters.  
  
I will create another pull request for this solution.

---
