# #437 - Implicit fallthroughs [Closed]

> Username: niklas88  
> Created at: 2017-06-08 08:22:11 UTC  
> Updated at: 2017-06-09 22:07:47 UTC  
> Closed at: 2017-06-09 22:07:47 UTC  
> Url: https://github.com/boostorg/beast/issues/437  

On master, when compiling with GCC 7.1.1 -Wimplicit-fallthrough is turned on and triggered by a lot of switch statements which by the looks of it are supposed to fall through (e.g. in serializer.ipp).  
  
Sadly the [[fallthrough]] attribute to mark these cases explicitly is only available from C++17 onwards. So I'm not sure what the best fix is.

---

## Comment 1

> Username: viccpp  
> Created at: 2017-06-08 11:01:48 UTC  
> Url: https://github.com/boostorg/beast/issues/437#issuecomment-307071239  

> So I'm not sure what the best fix is.  
  
-Wno-implicit-fallthrough ?

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-06-08 12:38:42 UTC  
> Url: https://github.com/boostorg/beast/issues/437#issuecomment-307090600  

I could add a macro  
```  
#define BEAST_FALLTHROUGH [[fallthrough]]  
```  
  
And then do the usual boost/config.hpp thing. Its better not to disable warnings. @glenfe is there a Boost config setting for fallthrough?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-06-08 12:49:06 UTC  
> Url: https://github.com/boostorg/beast/issues/437#issuecomment-307093043  

Oh hey!  
http://www.boost.org/doc/libs/1_64_0/libs/config/doc/html/boost_config/boost_macro_reference.html#boost_config.boost_macro_reference.macros_that_describe_c__17_features_not_supported  
  
`BOOST_FALLTHROUGH` is a thing!

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-06-08 12:55:35 UTC  
> Url: https://github.com/boostorg/beast/issues/437#issuecomment-307094619  

I tried to get GCC 7.x going on Travis but I am not familiar with Linux and the gcc packaging so I was unsuccessful. If someone wants to add a Travis matrix target for GCC 7.x that would really be helpful!

---

## Comment 5

> Username: glenfe  
> Created at: 2017-06-08 14:47:13 UTC  
> Url: https://github.com/boostorg/beast/issues/437#issuecomment-307126358  

The 1.64 definition of BOOST_FALLTHROUGH won't help you with gcc 7.1. We can make sure it will, for 1.65 though.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2017-06-08 20:06:12 UTC  
> Url: https://github.com/boostorg/beast/issues/437#issuecomment-307212532  

@2underscores-vic Please consider participating in the Boost formal review for Beast, which starts on July 1st. All you need to do is sign up to the Boost developers mailing list (https://lists.boost.org/mailman/listinfo.cgi/boost) ahead of time and listen for the announcement from the review manager. Your review is vital to the health and success of the library - whether you have positive things to say, or negative things to say, feedback is the only way for Beast to get better!
