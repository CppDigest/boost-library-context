# #57 Turn off BOOST_IOSTREAMS_HAS_DINKUMWARE_FPOS for MSVC++ [Merged]

> Username: BillyONeal  
> Created at: 2018-04-16 21:32:53 UTC  
> Updated at: 2020-04-02 05:33:39 UTC  
> Merged at: 2018-04-20 09:18:34 UTC  
> Closed at: 2018-04-20 09:18:34 UTC  
> Url: https://github.com/boostorg/iostreams/pull/57  

Boost is attempting to use a function not in the standard, seekpos(),  
guarded by this setting. Looks like Boost can use the standards  
conforming interface for MSVC++ instead. It should work going  
back effectively "forever" as far as MSVC++ is concerned, but this  
guard applies the change only to 2017. It's possible there is a better  
control in boost.config I missed.

---

## Comment 1

> Username: eldiener  
> Created_at: 2018-04-18 00:16:48 UTC  
> Url: https://github.com/boostorg/iostreams/pull/57#issuecomment-382200191  

The question is how we should be targeting vc++ 14.1 ? I think we should use BOOST_MSVC from config, which holds the value of vc++'s  _MSC_VER. Using a recent macro to identify a version of the vc++'s  standard library seems very chancy to me.

---

## Comment 2

> Username: BillyONeal  
> Created_at: 2018-04-18 00:45:14 UTC  
> Url: https://github.com/boostorg/iostreams/pull/57#issuecomment-382205643  

`_MSC_VER` should be OK if you'd prefer it; we added `_MSVC_STL_VERSION` specifically at the request of Boost for separate version numbers between the compiler and library (as sometimes new compilers get used with old libraries).

---

## Comment 3

> Username: eldiener  
> Created_at: 2018-04-18 01:07:24 UTC  
> Updated_at: 2018-04-18 01:07:58 UTC  
> Url: https://github.com/boostorg/iostreams/pull/57#issuecomment-382209905  

I would not use _MSC_VER directly in your PR but specifically BOOST_MSVC. This is because some other compilers, which are not vc++, also define _MSC_VER whereas BOOST_MSVC means that vc++ specifically is being used. Also are you really saying that newer versions of vc++ could be used with old versions of your C++ standard library ? I did not think that was possible in Microsoft distributions.  
  
When you say that 'Boost can use the standards conforming interface for MSVC++ instead. It should work going back effectively "forever" as far as MSVC++ is concerned' do you mean that all older versions of VC++, with their distributed versions of the C++ standard library, have a standards conforming interface as far as this problem is concerned ? That would be strange as it would put in question the original reason for the vc++ specific code in this particular situation.

---

## Comment 4

> Username: BillyONeal  
> Created_at: 2018-04-18 01:36:51 UTC  
> Updated_at: 2018-04-18 01:37:58 UTC  
> Url: https://github.com/boostorg/iostreams/pull/57#issuecomment-382216002  

> Also are you really saying that newer versions of vc++ could be used with old versions of your C++ standard library ?  
  
Yes, its a thing people do sometimes. Old libs with new compiler should work, old compiler with new libs is unsupported.  
  
> do you mean that all older versions of VC++, with their distributed versions of the C++ standard library, have a standards conforming interface as far as this problem is concerned ?  
  
Modulo bugs, yes. I only tested with 2017 though which is why I so guarded it that way.  
  
>That would be strange as it would put in question the original reason for the vc++ specific code in this particular situation.  
  
The Dinkumware code path is used for other Dinkumware licensees; they have this behavior of "call fsetpos followed by fseek" because the C standard says it's UB to call fsetpos on anything not returned by a call to fgetpos. (See C11, 7.21.9.3 "The fsetpos function"/2) But Dinkumware's caution there happens to be unnecessary for at least our current CRT implementation, where fsetpos just sets a 64 bit offset.  
  
There are some bugs that happened with VS 2017 15.7 and earlier where directly comparing fpos instances with non-`streamoff` integer types created ambiguities (compiler didn't know whether to convert fpos into streamoff and widen other integer, or convert other integer into fpos and compare those) which I fixed in 15.8 by adding comparison operators between fpos and all other integral types. (Note that 15.8 isn't even in preview yet; I just fixed this in our sources last week and found out about Boost depending on this nonstandard seekoff() member I removed because we build Boost with the prerelease compiler every night)

---

## Comment 5

> Username: BillyONeal  
> Created_at: 2018-04-18 01:42:40 UTC  
> Url: https://github.com/boostorg/iostreams/pull/57#issuecomment-382217283  

> directly comparing fpos instances with non-streamoff integer types  
  
Also note that the non-Dinkumware path in Boost already deals with this problem by directly casting the fpos to streamoff first.

---

## Comment 6

> Username: eldiener  
> Created_at: 2018-04-19 16:57:43 UTC  
> Updated_at: 2018-04-19 16:58:24 UTC  
> Url: https://github.com/boostorg/iostreams/pull/57#issuecomment-382807777  

The original code path is for Dinkumware rather than for vc++ specifically. If you are saying that vc++ always worked correctly with Dinkumware and did not need this one-off code path we could remove it for any version  of vc++ by just checking if BOOST_MSVC is defined. But to be more conservative than that, in order not to destroy the use of iostreams for past versions of vc++ other than the present one, I am fine with approving your PR as long as _MSVC_STL_VERSION is never used by any other compiler, which I assume and hope will be the case, without checking for BOOST_MSVC. To be really conservative though it might be best to check for both _MSVC_STL_VERSION and BOOST_MSVC as a reason to not use the code path. Can you change it appropriately to do that or do you want me to merge the PR and do it myself ? BOOST_MSVC is defined whenever vc++ is the compiler and not defined otherwise.

---

## Comment 7

> Username: BillyONeal  
> Created_at: 2018-04-19 21:55:07 UTC  
> Url: https://github.com/boostorg/iostreams/pull/57#issuecomment-382892834  

I was just informed last night that there's a Boost.IOStreams test failing right now; let me investigate and I'll keep you posted. Thanks!

---

## Comment 8

> Username: BillyONeal  
> Created_at: 2018-04-20 02:45:07 UTC  
> Url: https://github.com/boostorg/iostreams/pull/57#issuecomment-382952069  

Turns out the test failure is because I broke basic_stringbuf::seekoff last week :/, so not related to this change. I'll make the change you requested to test BOOST_MSVC first as soon as I hear I haven't broken boost.iostream any more. :)

---

## Comment 9

> Username: BillyONeal  
> Created_at: 2018-04-20 04:52:14 UTC  
> Url: https://github.com/boostorg/iostreams/pull/57#issuecomment-382975504  

OK, confirmed that it's just because I made seekoff corrupt basic_stringbuf which I just fixed. (Yay, thank you boost iostreams tests for catching my mistake!) Updated PR with the additional test you requested.

---

## Comment 10

> Username: MarcelRaad  
> Created_at: 2018-07-03 08:15:57 UTC  
> Url: https://github.com/boostorg/iostreams/pull/57#issuecomment-402052616  

Could this be merged to master please? This is needed for VS 2017 Update 8 as of Preview 3.0.

---

## Comment 11

> Username: garethsb  
> Created_at: 2018-07-09 09:11:02 UTC  
> Url: https://github.com/boostorg/iostreams/pull/57#issuecomment-403412898  

As the original author of the workarounds in boost/iostreams/positioning.hpp, I can confirm they absolutely were necessary back when I produced them in 2005, but that's a long time ago now, even for Boost. ;-)

---

## Comment 12

> Username: BillyONeal  
> Created_at: 2018-07-09 18:51:41 UTC  
> Url: https://github.com/boostorg/iostreams/pull/57#issuecomment-403582953  

I apologize on behalf of my predecessors? :P

---

## Comment 13

> Username: alanbirtles  
> Created_at: 2018-07-10 12:27:30 UTC  
> Url: https://github.com/boostorg/iostreams/pull/57#issuecomment-403803998  

No so long ago, this was only fixed in VS 2012, see https://groups.google.com/forum/#!searchin/boost-developers-archive/_FPOSOFF%7Csort:date/boost-developers-archive/VT7UxdJwfn4/MXpi6gyoBAAJ for some more discussion

---

## Comment 14

> Username: BillyONeal  
> Created_at: 2018-07-10 22:44:35 UTC  
> Url: https://github.com/boostorg/iostreams/pull/57#issuecomment-403989446  

Wait wait wait wait we cast it to long? WHY?!???! :(

---

## Comment 15

> Username: alanbirtles  
> Created_at: 2018-07-11 09:39:35 UTC  
> Url: https://github.com/boostorg/iostreams/pull/57#issuecomment-404108721  

Before VS2010 lots of ugly hacks were required for seeking past 2GB in std::fstream for similar reasons.

---

## Comment 16

> Username: mnordie  
> Created_at: 2018-10-16 09:34:11 UTC  
> Url: https://github.com/boostorg/iostreams/pull/57#issuecomment-430169705  

Any idea when this will be released?  
Doesn't seem to be in 1.68.

---

## Comment 17

> Username: jeking3  
> Created_at: 2018-10-16 11:43:11 UTC  
> Url: https://github.com/boostorg/iostreams/pull/57#issuecomment-430205532  

This should appear in 1.69.0 - I think it was supposed to be in 1.68.0 but wasn't merged in time.  It is in the pipeline now.

---

## Comment 18

> Username: mwpowellhtx  
> Created_at: 2018-10-30 23:52:30 UTC  
> Url: https://github.com/boostorg/iostreams/pull/57#issuecomment-434512644  

I'm receiving the same error in [iostreams/positioning.hpp](/boostorg/iostreams/blob/a3e973f3d49ce4f5469f83f759e528073b05726a/include/boost/iostreams/positioning.hpp#L96). Make sure y'all check and double check your regression before making that commit and/or merging, please.

---

## Comment 19

> Username: BillyONeal  
> Created_at: 2018-10-31 02:24:50 UTC  
> Url: https://github.com/boostorg/iostreams/pull/57#issuecomment-434538439  

@mwpowellhtx That should be guarded by the ifdef here? https://github.com/boostorg/iostreams/blob/a3e973f3d49ce4f5469f83f759e528073b05726a/include/boost/iostreams/positioning.hpp#L63

---

## Comment 20

> Username: mwpowellhtx  
> Created_at: 2018-10-31 03:31:28 UTC  
> Url: https://github.com/boostorg/iostreams/pull/57#issuecomment-434549114  

@BillyONeal It's a good question, it is, but how do I know whether I have *"Dinkumware fpos"*?  
  
Meanwhile, I am defining ``_SILENCE_FPOS_SEEKPOS_DEPRECATION_WARNING``, but letting the deprecated (returning zero) ``seekpos()`` linger just seems like asking for regression trouble.

---

## Comment 21

> Username: BillyONeal  
> Created_at: 2018-11-01 01:38:44 UTC  
> Url: https://github.com/boostorg/iostreams/pull/57#issuecomment-434902545  

@mwpowellhtx It would be more appropriate to call it "workaround broken Dinkumware behavior where they put file sizes into an int" :)

---

## Comment 22

> Username: mwpowellhtx  
> Created_at: 2018-11-01 01:45:57 UTC  
> Url: https://github.com/boostorg/iostreams/pull/57#issuecomment-434903660  

@BillyONeal Copy that; I understand. :+1:   
  
I installed the patch, BTW, and it appears to be working. I still have to "acknowledge" the issue, so to speak, but at least I can proceed forward.

---

## Comment 23

> Username: K-M-Ibrahim-Khalilullah  
> Created_at: 2020-04-02 05:33:39 UTC  
> Url: https://github.com/boostorg/iostreams/pull/57#issuecomment-607630666  

@BillyONeal   
would tell me please how can I turn off Boost_IOstream_has_dinkkumware_fpos? I already install boost with pcl!

---
