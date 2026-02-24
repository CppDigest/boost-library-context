# #439 - Wrongly guarded warning(push/pop) pragmas in cpp_int.hpp [Closed]

> Username: mariospr  
> Created at: 2022-03-04 10:53:12 UTC  
> Updated at: 2022-03-08 09:34:09 UTC  
> Closed at: 2022-03-08 09:34:09 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/439  

As in several other parts of the code base, [develop/include/boost/multiprecision/cpp_int.hpp](https://github.com/boostorg/multiprecision/blob/develop/include/boost/multiprecision/cpp_int.hpp) contains a pair of `warning(push) / warning(pop)` pragmas, but they are not correctly guarded in that the macro definitions guarding them don't match:  
```  
[...]  
  
namespace boost {  
namespace multiprecision {  
namespace backends {  
  
#ifdef BOOST_MSVC  
#pragma warning(push)  
#pragma warning(disable : 4307) // integral constant overflow (oveflow is in a branch not taken when it would overflow)  
#pragma warning(disable : 4127) // conditional expression is constant  
#pragma warning(disable : 4702) // Unreachable code (reachability depends on template params)  
#endif  
  
[...]  
  
#ifdef _MSC_VER  
#pragma warning(pop)  
#endif  
  
}} // namespace boost::multiprecision  
  
[...]  
```  
  
As a result of this, if you're building on Windows using a compiler that won't get `BOOST_MSVC` defined but still will get `_MSC_VER` defined, and you're also building with `-Werror -Wunknown-pragmas`, you'd get an error like this one:  
```  
third_party/boost/multiprecision/include\boost/multiprecision/cpp_int.hpp(2334,20): error: pragma diagnostic pop could not pop, no matching push [-Werror,-Wunknown-pragmas]  
#pragma warning(pop)  
                   ^  
1 error generated.  
ninja: build stopped: subcommand failed.  
```  
  
Looking into every other file that uses these pragmas, I can see that all of them either guard both pragmas with `#ifdef BOOST_MSVC` (e.g. [number.hpp](https://github.com/boostorg/multiprecision/blob/develop/include/boost/multiprecision/number.hpp)) or with `#ifdef _MSC_VER` (e.g.  [cpp_int/multiply.hpp](https://github.com/boostorg/multiprecision/blob/develop/include/boost/multiprecision/cpp_int/multiply.hpp)), but never with a mix of them, so it seems to be that this mismatch is unintentional and a bug.  
  
Now, precisely because I can see the two alternatives throughout the code (`_MSC_VER` used in `import_export.hpp`, `bitwise.hpp`, `misc.hpp`, `limits.hpp` and `multiply.hpp`, inside `cpp_int/`, and then `BOOST_MSVC` everywhere else), I'm not 100% sure of which one should be used in cpp_int.hpp (probably `BOOST_MSVC`, I guess?), so reporting the bug for now for someone else to apply the correct fix.  
  
Thanks!

---

## Comment 1

> Username: ckormanyos  
> Created at: 2022-03-04 20:47:31 UTC  
> Updated at: 2022-03-04 20:48:31 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/439#issuecomment-1059519368  

> I'm not 100% sure of which one should be used in cpp_int.hpp (probably `BOOST_MSVC` everywhere else)  
  
Great catch. Thanks for this report. There are two topics.  
- Which to use of either `BOOST_MSVC` or `_MSC_VER`?  
- When decided, use them consistently in all warning push/pop sequences  
  
@jzmaddock which one of `BOOST_MSVC` or `_MSC_VER` should we use? I can handle the edits if you like.  
  
Also, John, do you consider this a change that should be dealt with for 1.79 or has that ship already sailed?

---

## Comment 2

> Username: ckormanyos  
> Created at: 2022-03-04 20:48:35 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/439#issuecomment-1059519964  

> As a result of this, if you're building on Windows using a compiler that won't get `BOOST_MSVC` defined but still will get `_MSC_VER` defined  
  
@mariospr do you have an example which compiler(s) you mean?

---

## Comment 3

> Username: mariospr  
> Created at: 2022-03-05 23:26:25 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/439#issuecomment-1059850082  

I was building Chromium-based browser [Brave](https://github.com/brave/brave-browser) which, like Chromium, relies on Clang (Clang 15, in the particular version of Chromium I was working wiht) to be built both on Windows and other platforms, and uses both `-Werror` and `-Wunknown-pragmas` build flags..  
  
More specifically, I was trying to build a not-yet-released version of Brave based on the upcoming Chromium 100 release (see [branch cr100 from the brave-core repo here](https://github.com/brave/brave-core/commits/cr100)) on Windows after integrating Boost's multiprecision library to support 256-bit integers for the "Brave Wallet" feature when I found the issue, which didn't happen on other platforms I tried such as Android or Linux (which built just fine with Boost's multiprecision without further changes).  
  
Should you want to test this yourself, you can follow the general instructions at https://github.com/brave/brave-browser/blob/master/README.md and the Windows-specific ones at https://github.com/brave/brave-browser/wiki/Windows-Development-Environment, but making sure you build [the `cr100` branch of brave-core](https://github.com/brave/brave-core/commits/cr100) instead of `master`, and  reverting the [patch that disabled the `-Wunknown-pragmas` flag](https://github.com/brave/brave-core/commit/5222ab4308c26192e7e2dd9e0ddd2cbc540ce15b  
) on top of it, and you should be all set.  
  
Building a Chromium-based browser is a bit of a convoluted process, so please let me know if you need any extra help with that and I'll be happy to help to the extent of my abilities.  
  
Thanks a lot for considering my report so promptly!

---

## Comment 4

> Username: jzmaddock  
> Created at: 2022-03-06 09:34:39 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/439#issuecomment-1059927704  

Obviously we should be consistent here, I would tend to vote for BOOST_MSVC throughout.

---

## Comment 5

> Username: mariospr  
> Created at: 2022-03-06 09:54:01 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/439#issuecomment-1059930314  

> Obviously we should be consistent here, I would tend to vote for BOOST_MSVC throughout.  
  
Thanks for confirming. I've prepared a PR in https://github.com/boostorg/multiprecision/pull/440, in case you want to review it and merge it already, after looking for all instances of `warning(push/pop)` pragmas adjusting the guards where needed.

---

## Comment 6

> Username: mariospr  
> Created at: 2022-03-08 09:32:47 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/439#issuecomment-1061579356  

Not sure what the right process is, but since https://github.com/boostorg/multiprecision/pull/440 has been merged, I suppose this issue could perhaps be closed?

---

## Comment 7

> Username: jzmaddock  
> Created at: 2022-03-08 09:34:09 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/439#issuecomment-1061580536  

Yep.
