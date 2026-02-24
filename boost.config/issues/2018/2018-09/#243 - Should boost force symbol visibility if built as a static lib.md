# #243 - Should boost force symbol visibility if built as a static lib? [Closed]

> Username: epvbergen  
> Created at: 2018-09-25 07:13:26 UTC  
> Updated at: 2022-08-02 09:27:18 UTC  
> Closed at: 2018-09-25 07:47:49 UTC  
> Url: https://github.com/boostorg/config/issues/243  

I'm consuming boost::asio as a static library in a project that is delivered as a shared library. To avoid conflicts in clients, I try to hide all symbols originating from my dependencies by linking with -Wl,--exclude-libs,ALL.  
  
To my surprise, this does not work for boost::asio. A little research showed that at some point it was deemed a good idea to force default visibility on exception typeinfo and support classes for printing the exceptions from boost, see eg. https://svn.boost.org/trac10/ticket/4594 which proposed a #pragma visibility push default, only conditional on the compiler. So regardless of whether boost is built as a static or a dynamic library, the symbols get marked with default visibility on GCC.  
  
And now I wonder if this is really the correct policy, or if boost (and any library) should rather refrain from explicitly setting visibility attributes when building a static library. Doesn't that make more sense?  
  
Right now I have a nasty symbol leak that causes conflicts in my clients using a different version of asio. Updated: the problem has been reported before, along with a possible solution, see https://svn.boost.org/trac10/ticket/12722.  
  
N.B.: moved here from https://github.com/boostorg/asio/issues/145

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-09-25 07:47:49 UTC  
> Url: https://github.com/boostorg/config/issues/243#issuecomment-424241293  

Confirmed, but this is an issue with throw_exception and should be reported here: https://github.com/boostorg/throw_exception.  That header should be using BOOST_SYMBOL_VISIBLE rather than the push/pop's.  I would guess that their workaround pre-dates the introduction of BOOST_SYMBOL_VISIBLE.

---

## Comment 2

> Username: epvbergen  
> Created at: 2018-09-25 13:59:23 UTC  
> Url: https://github.com/boostorg/config/issues/243#issuecomment-424354582  

I've reported it there and proposed a small patch to replace the push/pops with BOOST_SYMBOL_VISIBLE.  
  
However, that only seems to be half the solution, since BOOST_SYMBOL_VISIBLE is always set to __attribute__((__visibility__("default"))) in config/compiler/gcc.hpp if __GNUC__ >= 4, regardless of whether the boost build system is building a shared library or not.  
  
In the end I need the ability to build my boost-using shared library with -fvisibility=hidden and -fvisibility=hidden-inlines, without individual symbols from Boost forcing themselves out of my interface.  
  
I guess somehow or other, that implies adding an extra condition before setting BOOST_SYMBOL_VISIBLE to whatever the compiler needs.  
  
A possibility could be a BOOST_INTERFACE_VISIBLE macro that either Boost itself can set when Boost is building a shared library, or that the user can set if she uses boost classes as part of the interface of her own shared library. What do you think?

---

## Comment 3

> Username: jzmaddock  
> Created at: 2018-09-25 17:01:32 UTC  
> Url: https://github.com/boostorg/config/issues/243#issuecomment-424422977  

Ah my bad, we have an issue here, because there are 2 conflicting use cases:  
  
* Using boost - we want exceptions to be visible types, otherwise we can't catch them.  
* Hiding boost as an internal dependency - where we want all boost symbols to be hidden.  
  
I'm not sure that we've ever really supported the second case - except as a sort of accident by having code that's not marked up.  
  
I'll raise this on the mailing list, it's probably better to try and fix boost-wide (if we can) rather than each library doing it's own thing.

---

## Comment 4

> Username: epvbergen  
> Created at: 2019-05-17 13:45:28 UTC  
> Url: https://github.com/boostorg/config/issues/243#issuecomment-493459088  

Any feedback on this?

---

## Comment 5

> Username: jzmaddock  
> Created at: 2019-05-19 07:29:49 UTC  
> Url: https://github.com/boostorg/config/issues/243#issuecomment-493733960  

I have an idea, but I'm working on another bug report right now.... nag me if I'm not back to you shortly...

---

## Comment 6

> Username: jzmaddock  
> Created at: 2019-05-19 11:11:10 UTC  
> Url: https://github.com/boostorg/config/issues/243#issuecomment-493747861  

My suggestion was going to be a macro - say BOOST_DISABLE_VISIBLE_SYMBOLS - which when set by the user would result in the BOOST_SYMBOL_* macros being set to nothing.  That would then allow you to hide everything.  I have a suspicion that some things (exception classes and some of the serialization lib) will not then work correctly, but I'm not sure?

---

## Comment 7

> Username: Lastique  
> Created at: 2019-05-19 13:25:49 UTC  
> Updated at: 2019-05-19 13:26:36 UTC  
> Url: https://github.com/boostorg/config/issues/243#issuecomment-493757343  

I don't think this is a good idea. This may break exceptions and RTTI support. Boost.Log relies on this, for example. Other libraries, probably, too (Boost.Any comes to mind).  
  
One example where making all symbols hidden may make sense is when a user builds a monolithic executable that does not use any other shared libraries, or uses them without passing any Boost-related types, including exceptions. Those cases can be covered by linker scripts, there's no need to change Boost for that.  
  
The particular issue with Boost.ASIO though should be reported to [upstream ASIO](https://think-async.com/Asio/). I see no reason why it should mark *all* of its symbols visible (see [here](https://github.com/boostorg/asio/blob/db6423c9b1bd5c5ff4f7ea391446c7ac593ce549/include/boost/asio/detail/push_options.hpp#L28) and [here](https://github.com/boostorg/asio/blob/db6423c9b1bd5c5ff4f7ea391446c7ac593ce549/include/boost/asio/detail/pop_options.hpp#L28)). But it may need to mark so exceptions, selectively. This is not a Boost.Config issue and changing Boost.Config will not fix Boost.ASIO and it will break other libraries.

---

## Comment 8

> Username: uecasm  
> Created at: 2019-05-20 02:02:06 UTC  
> Updated at: 2019-05-20 02:04:54 UTC  
> Url: https://github.com/boostorg/config/issues/243#issuecomment-493815832  

If you are wanting to hide all internal usage of Boost, and you are:  
  
1. Building a single shared library, and not a static library.  
2. Using Boost as a static library, not as shared libraries.  
3. Not using Boost types in your own library's interface (at all; not even in "private" parts of public header files).  (This may also require you to use explicit visibility for your own library's symbols too.)  
4. Catching all Boost exceptions at your interface boundary and converting them to some other (visible) exception type.  
  
Then it is probably safe (and correct) to disable Boost symbol visibility across the board.  Violating any one of those four constraints would break things, however.

---

## Comment 9

> Username: nigels-com  
> Created at: 2021-12-16 02:32:53 UTC  
> Url: https://github.com/boostorg/config/issues/243#issuecomment-995384315  

I meet all four criteria above.  Question is how to force all the symbols private?

---

## Comment 10

> Username: Lastique  
> Created at: 2021-12-16 10:31:22 UTC  
> Url: https://github.com/boostorg/config/issues/243#issuecomment-995643032  

Use [linker scripts](https://sourceware.org/binutils/docs/ld/VERSION.html).

---

## Comment 11

> Username: weglimir  
> Created at: 2022-04-24 08:50:23 UTC  
> Url: https://github.com/boostorg/config/issues/243#issuecomment-1107793436  

I came accross exactly the same problem, where our dynamic library is one of huge amount of custom libraries in multi plug-in environment. Disabling ASIO symbols with macro switch partially helped, as all symbols in header like boost system still use BOOST_SYMBOL_VISIBLE and this caused to crash in my case by throwing an exception from asio. I guess this case is quite uncommon, as in my example there are 100 dynamic libs which work in independent way, and are not interacting with each other at all. That being said I think it would be very helpfull if there would be any way to simply diable BOOST_SYMBOL_VISIBLE by overriding it to nothing during compilation.

---

## Comment 12

> Username: jcelerier  
> Created at: 2022-08-02 09:04:17 UTC  
> Url: https://github.com/boostorg/config/issues/243#issuecomment-1202217714  

> Use [linker scripts](https://sourceware.org/binutils/docs/ld/VERSION.html).  
  
that only works on platforms which supports those. What's wrong with just adding a  `BOOST_HIDE_ALL_SYMBOLS` macro which would define `BOOST_SYMBOL_EXPORT` and  `BOOST_SYMBOL_VISIBLE` to the relevant platform attribute for that ?   
  
in addition, doing this at the linker stage prevents compiler optimizations that could be done based on symbol visibility.

---

## Comment 13

> Username: jcelerier  
> Created at: 2022-08-02 09:27:18 UTC  
> Url: https://github.com/boostorg/config/issues/243#issuecomment-1202243325  

See #441 for a patch
