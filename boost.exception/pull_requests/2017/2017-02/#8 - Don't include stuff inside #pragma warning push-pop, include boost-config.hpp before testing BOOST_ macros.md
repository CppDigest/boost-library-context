# #8 Don't include stuff inside #pragma warning push/pop, include boost/config.hpp before testing BOOST_ macros. [Closed]

> Username: pgroke-dt  
> Created at: 2017-02-15 14:49:52 UTC  
> Updated at: 2017-05-05 16:38:55 UTC  
> Closed at: 2017-02-21 02:56:23 UTC  
> Url: https://github.com/boostorg/exception/pull/8  

I've moved some #include lines around + added #include <boost/config.hpp> lines to some files to avoid...  
- testing BOOST_* macros before config.hpp was included  
- including other header files inside #pragma warning push/pop pairs  
  
We're building with warnings=errors, and including e.g. <boost/config.hpp> for the first time inside a #pragma warning push/pop pair means all #pragma warning disable that are done in config.hpp will be undone by the pop. Which means our builds will fail because of some upgraded warning. Also I think it's generally not a good idea to include files inside a #pragma warning push/pop pair.  
  
And of course every file that tests/uses some BOOST_* macro should include <boost/config.hpp> first, to e.g. give Boost.Config and/or <config/user.hpp> the change to define that macro.

---

## Comment 1

> Username: zajo  
> Created_at: 2017-02-21 02:56:23 UTC  
> Url: https://github.com/boostorg/exception/pull/8#issuecomment-281231600  

Please see current develop branch, I didn't directly merge your changes but mine should address the problem. Let me know if they do not.

---

## Comment 2

> Username: pgroke-dt  
> Created_at: 2017-02-21 16:54:11 UTC  
> Url: https://github.com/boostorg/exception/pull/8#issuecomment-281404710  

They do not ;)  
  
One of the problems is still present in _current_exception_cast.hpp_ and _detail/is_output_streamable.hpp_: both don't include _config.hpp_ but then test the macro _BOOST_EXCEPTION_ENABLE_WARNINGS_ -- which the user might want to define in _user.hpp_.  
  
Aside from that, _info.hpp_ includes _config.hpp_ twice now, and a couple of others only include _config.hpp_ by proxy. Those aren't real bugs/problems of course, but personally, if it were my project, I'd still change it.  
  
All in all I don't fully understand why you didn't just merge my changes. Aside from what I mentioned above my commit is virtually identical to yours, so... hmmm.

---

## Comment 3

> Username: zajo  
> Created_at: 2017-02-21 18:26:26 UTC  
> Url: https://github.com/boostorg/exception/pull/8#issuecomment-281432525  

BOOST_EXCEPTION_ENABLE_WARNINGS is not documented and users should not define it. See www.boost.org/doc/libs/release/libs/exception/doc/configuration_macros.html.

---

## Comment 4

> Username: pgroke-dt  
> Created_at: 2017-02-21 18:36:49 UTC  
> Updated_at: 2017-02-21 18:39:36 UTC  
> Url: https://github.com/boostorg/exception/pull/8#issuecomment-281435486  

OK. The other stuff is still broken though. (EDIT: Er, strike that, there is no other stuff, except the other stuff in the throw_exception repo.)  
  
And regardless of who/where it should or shouldn't be defined, I think consistent behavior/handling across all "exception" headers would still be preferable, wouldn't it?  
  
Side note: the issues in throw_exception also still remain (e.g. including &lt;memory> inside the warning push/pop).

---

## Comment 5

> Username: zajo  
> Created_at: 2017-02-21 19:56:27 UTC  
> Url: https://github.com/boostorg/exception/pull/8#issuecomment-281461402  

I removed the double-inclusion of config.hpp in info.hpp, thank you.  
  
Throw_exception.hpp now includes config.hpp outside of the push/pop. This header is critical, we need to be conservative with making any changes in it. What is your concern? Note that workaround.hpp contains only macros.  
  
Headers that use the rest of the (supported) BOOST_XXXX or BOOST_EXCEPTION_XXXX configuration macros now include config.hpp. I don't think I've missed any, but do let me know if I did.

---

## Comment 6

> Username: pgroke-dt  
> Created_at: 2017-02-21 20:32:32 UTC  
> Url: https://github.com/boostorg/exception/pull/8#issuecomment-281471128  

I think there shouldn't be *any* includes inside a pragma warning push/pop - in general, not only in this library. Includes from the same project might be OK-ish. But includes from other Boost libraries or even compiler/library/platform headers are dangerous. E.g. there are includes of &lt;exception> or &lt;boost/current_function.hpp> inside the pusp/pop. Both are outside the "throw_exception" project, so we cannot know and/or control what other headers they pull in. Meaning the pop could potentially undo pragma warning changes from a lot of other, unrelated header files.  
  
The same applies to _exception.hpp_, which includes &lt;memory>.  
  
Maybe that's not terribly important, but it's the reason why I took the time to shuffle around the include lines to what I guess makes sense. I also patched a 1.63 copy with the changes and executed all "exception" tests locally to make sure I didn't commit some complete BS :) (I did not execute *all* Boost tests however, and also I only tested with MSVC 2015)  
  
ps: The changes you made so far are certainly enough for our project. It's just that I'm kind-of a perfectionist and when I touch something I really like to fix it thoroughly. Hope you don't mind that I'm being so tenacious.

---

## Comment 7

> Username: zajo  
> Created_at: 2017-02-22 00:08:59 UTC  
> Url: https://github.com/boostorg/exception/pull/8#issuecomment-281524450  

Thanks for spotting the issue with boost/config.hpp. FYI, there is no official Boost policy about warnings.

---

## Comment 8

> Username: pgroke-dt  
> Created_at: 2017-02-22 00:23:59 UTC  
> Url: https://github.com/boostorg/exception/pull/8#issuecomment-281527227  

Well, bummer! Maybe there should be ;)

---

## Comment 9

> Username: pgroke-dt  
> Created_at: 2017-05-05 16:38:55 UTC  
> Url: https://github.com/boostorg/exception/pull/8#issuecomment-299514241  

Hi,  
is there anything blocking your "#including config.hpp before other headers" commit here, and the accompanying one in throw_exception, from being merged to master right now? Because it would be cool if this would make it into 1.65.  
  
Thanks,  
Paul

---
