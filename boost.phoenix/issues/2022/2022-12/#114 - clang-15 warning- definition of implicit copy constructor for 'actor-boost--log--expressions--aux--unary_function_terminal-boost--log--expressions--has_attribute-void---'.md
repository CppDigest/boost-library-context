# #114 - clang-15 warning: definition of implicit copy constructor for 'actor<boost::log::expressions::aux::unary_function_terminal<boost::log::expressions::has_attribute<void>>>' is deprecated because it has a user-declared copy assignment operator [Open]

> Username: k15tfu  
> Created at: 2022-12-21 23:21:27 UTC  
> Updated at: 2023-03-07 14:52:50 UTC  
> Url: https://github.com/boostorg/phoenix/issues/114  

Hi!  
  
In file boost/phoenix/core/actor.hpp:  
```  
boost/phoenix/core/actor.hpp:228:39: error: definition of implicit copy constructor for 'actor<boost::log::expressions::aux::unary_function_terminal<boost::log::expressions::has_attribute<void>>>' is deprecated because it has a user-declared copy assignment operator [-Werror,-Wdeprecated-copy]  
        BOOST_DELETED_FUNCTION(actor& operator=(actor const&))  
                                      ^  
[...]  
1 error generated.  
```

---

## Comment 1

> Username: djowel  
> Created at: 2022-12-22 00:24:44 UTC  
> Url: https://github.com/boostorg/phoenix/issues/114#issuecomment-1362249184  

Instead of copy-pasting the warning message, how about analyzing the actual problem and telling why this is a phoenix issue?

---

## Comment 2

> Username: nickwilliams-zaxiom  
> Created at: 2023-03-07 14:36:23 UTC  
> Url: https://github.com/boostorg/phoenix/issues/114#issuecomment-1458284450  

I'm also seeing this in clang-14.

---

## Comment 3

> Username: Kojoley  
> Created at: 2023-03-07 14:46:30 UTC  
> Url: https://github.com/boostorg/phoenix/issues/114#issuecomment-1458300683  

I'm confident that it was fixed in 1.81 by https://github.com/boostorg/phoenix/pull/108. If you still experience the warning - please post Boost version, compiler version, compiler command line and a reproducer.

---

## Comment 4

> Username: nickwilliams-zaxiom  
> Created at: 2023-03-07 14:52:42 UTC  
> Updated at: 2023-03-07 14:52:50 UTC  
> Url: https://github.com/boostorg/phoenix/issues/114#issuecomment-1458310312  

Thanks. I was able to fix our warning by putting those pragmas around our import of `qi.hpp`.
