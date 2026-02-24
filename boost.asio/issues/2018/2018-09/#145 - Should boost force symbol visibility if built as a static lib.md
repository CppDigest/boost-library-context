# #145 - Should boost force symbol visibility if built as a static lib? [Closed]

> Username: epvbergen  
> Created at: 2018-09-20 10:50:48 UTC  
> Updated at: 2018-09-25 14:09:55 UTC  
> Closed at: 2018-09-25 07:13:54 UTC  
> Url: https://github.com/boostorg/asio/issues/145  

I'm consuming boost::asio as a static library in a project that is delivered as a shared library. To avoid conflicts in clients, I try to hide all symbols originating from my dependencies by linking with -Wl,--exclude-libs,ALL.  
  
To my surprise, this does not work for boost::asio. A little research showed that at some point it was deemed a good idea to force default visibility on exception typeinfo and support classes for printing the exceptions from boost, see eg. https://svn.boost.org/trac10/ticket/4594 which proposed a #pragma visibility push default, only conditional on the compiler. So regardless of whether boost is built as a static or a dynamic library, the symbols get marked with default visibility on GCC.  
  
And now I wonder if this is really the correct policy, or if boost (and any library) should rather refrain from explicitly setting visibility attributes when building a static library. Doesn't that make more sense?  
  
Right now I have a nasty symbol leak that causes conflicts in my clients using a different version of asio. Updated: the problem has been reported before, along with a possible solution, see https://svn.boost.org/trac10/ticket/12722.

---

## Comment 1

> Username: apolukhin  
> Created at: 2018-09-20 20:11:55 UTC  
> Url: https://github.com/boostorg/asio/issues/145#issuecomment-423317013  

Good point. Could you please move that issue to the https://github.com/boostorg/config repo. Fixing it there will fix all the boost libraries in one go.

---

## Comment 2

> Username: epvbergen  
> Created at: 2018-09-25 07:13:54 UTC  
> Url: https://github.com/boostorg/asio/issues/145#issuecomment-424232422  

Thanks, it's now at https://github.com/boostorg/config/issues/243

---

## Comment 3

> Username: mzeren-vmw  
> Created at: 2018-09-25 14:09:55 UTC  
> Url: https://github.com/boostorg/asio/issues/145#issuecomment-424358573  

I'm a bit confused. How does moving this bug to config fix:  
https://github.com/boostorg/asio/blob/develop/include/boost/asio/detail/push_options.hpp#L26  
See also: https://svn.boost.org/trac10/ticket/12722  
Which we workaround by patching our Asio build to remove the visibility pragmas.
