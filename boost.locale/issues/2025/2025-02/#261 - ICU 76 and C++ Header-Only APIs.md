# #261 - ICU 76 and C++ Header-Only APIs [Open]

> Username: Project579  
> Created at: 2025-02-18 21:21:24 UTC  
> Updated at: 2025-02-26 12:36:34 UTC  
> Url: https://github.com/boostorg/locale/issues/261  

The ICU unstable C++ ABI has been a huge pain for me and many other developers, it seems the project has finally found a solution to the issue, obviously dependent libraries will have to be updated, in my case the most used one is `boost::locale`.  
  
It would be very nice if `boost::locale` could be converted to the new C++ Header-Only APIs, this would allow updating ICU without needing to recompile every project that depends on it.  
  
This is likely going to be a breaking change as I assume all ICU versions before 76 won't be compatible with the new API.

---

## Comment 1

> Username: artyom-beilis  
> Created at: 2025-02-18 21:47:57 UTC  
> Url: https://github.com/boostorg/locale/issues/261#issuecomment-2667000238  

Removed previous comment due to improperly understanding the request.

---

## Comment 2

> Username: Flamefire  
> Created at: 2025-02-19 09:51:04 UTC  
> Updated at: 2025-02-26 12:36:34 UTC  
> Url: https://github.com/boostorg/locale/issues/261#issuecomment-2668097354  

I don't fully understand this. What I got:  
- ICU uses C++ classes declared in the header and at least partially defined in the sources  
- ICU changes the declarations of the classes in a way that breaks ABI, e.g. adding or removing fields.  
- projects compiled against one version of ICU don't work when run with another version of ICU  
  
This is pretty common for C++ and I don't see what can be done here to solve this.     
IIRC ICU uses versioned namespaces to avoid classes of one version conflicting with another ICU version, which IMO is as good as it gets as you'll get at least a linker error when trying to use something incompatible.  
  
What exactly are those "new C++ Header-Only APIs" and what do they solve?     
Even Boost.Locale uses ICU in source files and won't move all those to the headers for practical reasons.  
  
I also think going from a minimal requirement of ICU 4.8.1 to 76 is too much. Especially as Ubuntu 22.04 ships with ICU 70, Ubuntu 20.04 has ICU 66, so this would drop support for too many OSes

---

## Comment 3

> Username: Project579  
> Created at: 2025-02-19 19:38:33 UTC  
> Url: https://github.com/boostorg/locale/issues/261#issuecomment-2669587750  

Please see the ICU 76 documentation for details on the new API: [c-header-only-apis](https://github.com/unicode-org/icu/blob/579840539f37374e680477424e369200505c9131/docs/download/76.md#c-header-only-apis)  
  
> I also think going from a minimal requirement of ICU 4.8.1 to 76 is too much. Especially as Ubuntu 22.04 ships with ICU 70, Ubuntu 20.04 has ICU 76, so this would drop support for too many OSes  
  
It does not necessarily have to drop support, it just needs to be able to detect ICU 76+ and use the new ABI stable APIs in that case, the big advantage is especially for Operating System maintainers but also users in general as we won't have to rebuild half of our packages every time ICU updates.

---

## Comment 4

> Username: Flamefire  
> Created at: 2025-02-20 09:10:29 UTC  
> Url: https://github.com/boostorg/locale/issues/261#issuecomment-2670880358  

Ok so if I understand that correctly we can define `U_SHOW_CPLUSPLUS_API=0` and `U_SHOW_CPLUSPLUS_HEADER_API=1` when ICU >= 76 is detected and use appropriate typedefs if the interface is at least similar enough.  
  
With that it is supposed to work with any ICU >= 76 binary even when ICU is used in the implementation files.  
  
I'm not fully sure about that as there might be references to versioned ICU binaries. But that is likely up to users in how they compile Boost.Locale, specifically against which ICU libs they link.  
  
We can check if that approach is feasible. There are some places where "hacks" are used to get the desired behavior out of ICU and if that relies on the non-header API we can't do that switch.  
  
May I ask what your role/use case is exactly? Are you asking this on behalf of a specific OS, or a project in general? I just want to understand where you are coming from and who, if any, might want to weight in here too. E.g. @jwakely notified us earlier that some larger changes do affect downstream users very much and this might be such a change
