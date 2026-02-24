# #9 - Should boost force symbol visibility if built as a static lib? [Closed]

> Username: epvbergen  
> Created at: 2018-09-25 08:09:43 UTC  
> Updated at: 2018-09-28 13:09:10 UTC  
> Closed at: 2018-09-27 12:26:58 UTC  
> Url: https://github.com/boostorg/throw_exception/issues/9  

I'm consuming boost::asio as a static library in a project that is delivered as a shared library. To avoid conflicts in clients, I try to hide all symbols originating from my dependencies by linking with -Wl,--exclude-libs,ALL.  
  
To my surprise, this does not work for boost::asio. A little research showed that at some point it was deemed a good idea to force default visibility on exception typeinfo and support classes for printing the exceptions from boost, see eg. https://svn.boost.org/trac10/ticket/4594 which proposed a #pragma visibility push default, only conditional on the compiler. So regardless of whether boost is built as a static or a dynamic library, the symbols get marked with default visibility on GCC.  
  
And now I wonder if this is really the correct policy, or if boost (and any library) should rather refrain from explicitly setting visibility attributes when building a static library. Doesn't that make more sense?  
  
Right now I have a nasty symbol leak that causes conflicts in my clients using a different version of asio. Updated: the problem has been reported before, along with a possible solution, see https://svn.boost.org/trac10/ticket/12722.  
  
N.B.: moved here from https://github.com/boostorg/config/issues/243

---

## Comment 1

> Username: pdimov  
> Created at: 2018-09-25 18:02:07 UTC  
> Url: https://github.com/boostorg/throw_exception/issues/9#issuecomment-424443065  

If exceptions are hidden, they can't be caught. Whether we're in a static library or not is not enough information; the static library may later be used as part of a shared library. (There are other problems with visibility too - the undefined behavior sanitizer can't validate virtual calls when the vtable/typeinfo are hidden, so all polymorphic classes used outside the library need to be visible too. Hidden visibility is quite tricky.)

---

## Comment 2

> Username: pdimov  
> Created at: 2018-09-27 12:26:58 UTC  
> Url: https://github.com/boostorg/throw_exception/issues/9#issuecomment-425071423  

I assume that merging #10 has resolved this issue. The visibility of the exception classes is now controlled by BOOST_SYMBOL_VISIBLE, which is defined by Config.

---

## Comment 3

> Username: epvbergen  
> Created at: 2018-09-28 09:21:17 UTC  
> Url: https://github.com/boostorg/throw_exception/issues/9#issuecomment-425376546  

Thanks, however please note the comment regarding placement at https://github.com/boostorg/throw_exception/pull/10#issuecomment-424443436, as the patch would now break Windows DLL builds. Could I have flagged the merge request in some way to prevent a premature merge? The point was to solicit comments on a proposed patch.  
  
You write "the static library may later be used as part of a shared library", and precisely there I would suggest it is up to the shared library's author to make the decision to either expose (some of) Boost, which indeed implies having to expose Boost's vtables and typeinfo as well as you say, or hide everything, including any exceptions. The latter corresponds to my use case.   
  
You're right in that it's not enough information to know what's being built, you need that decision from the user as well. That's why in Config I proposed a  BOOST_INTERFACE_VISIBLE macro that either Boost itself can set when Boost is building a shared library, or that the user can set if she uses boost classes as part of the interface of her own shared library. See https://github.com/boostorg/config/issues/243.   
  
Despite its trickiness, it seems that hiding everything is the only way to get a shared-library SDK or plugin using Boost to work in environments that uses different or differently built versions of Boost?

---

## Comment 4

> Username: pdimov  
> Created at: 2018-09-28 13:06:29 UTC  
> Url: https://github.com/boostorg/throw_exception/issues/9#issuecomment-425429600  

> Thanks, however please note the comment regarding placement at #10 (comment)#10, Replaced hardcoded symbol visibility for GCC with BOOST_SYMBOL_VISIBLE, as the patch would now break Windows DLL builds.  
  
https://github.com/boostorg/throw_exception/commit/fe0f62bf1726cbc503ac664c24ac187bff789aae

---

## Comment 5

> Username: pdimov  
> Created at: 2018-09-28 13:09:10 UTC  
> Url: https://github.com/boostorg/throw_exception/issues/9#issuecomment-425430310  

What I meant is that this is no longer an issue concerning `throw_exception`; it will be a decision controlled by Config, presumably through a user-defined macro that disables BOOST_SYMBOL_VISIBLE. This will then take effect throughout Boost.
