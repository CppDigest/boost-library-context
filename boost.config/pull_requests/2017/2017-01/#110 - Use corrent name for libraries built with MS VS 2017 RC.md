# #110 Use corrent name for libraries built with MS VS 2017 RC. [Merged]

> Username: o01eg  
> Created at: 2017-01-05 18:21:58 UTC  
> Updated at: 2017-02-04 15:42:45 UTC  
> Merged at: 2017-02-04 13:22:16 UTC  
> Closed at: 2017-02-04 13:22:16 UTC  
> Url: https://github.com/boostorg/config/pull/110  

Boost built with MS VS 2017 RC has -vc150 suffix.

---

## Comment 1

> Username: eldiener  
> Created_at: 2017-01-05 21:13:49 UTC  
> Url: https://github.com/boostorg/config/pull/110#issuecomment-270759247  

Is 1910 the minimum _MSC_VER for VS 2017 ?

---

## Comment 2

> Username: jhunold  
> Created_at: 2017-01-05 21:36:26 UTC  
> Url: https://github.com/boostorg/config/pull/110#issuecomment-270764578  

Yes, the official version is 1910. I'm not sure if the correct library suffix should be -141 instead, as the compiler itself is still ABI-compatible with vc14. And the Boost.Build support for this is still flaky. I'm not sure I'll have the time to get it stable, though.

---

## Comment 3

> Username: eldiener  
> Created_at: 2017-01-05 22:47:44 UTC  
> Url: https://github.com/boostorg/config/pull/110#issuecomment-270780369  

Then maybe we should just wait until VS 2017 is officially released instead of hacking away at it and not really knowing what it will be until it is released.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2017-01-06 18:07:15 UTC  
> Url: https://github.com/boostorg/config/pull/110#issuecomment-270965036  

Do we know if Microsoft are calling it VC15 or VC14.1?

---

## Comment 5

> Username: o01eg  
> Created_at: 2017-01-06 18:27:28 UTC  
> Url: https://github.com/boostorg/config/pull/110#issuecomment-270969642  

It defined in msvc.jam from Boost.Build in https://github.com/boostorg/build/commit/ae5e63a1315493dfb2fc60c4d537674fa637f898 which was released in 1.63.0 version.

---

## Comment 6

> Username: KindDragon  
> Created_at: 2017-02-03 00:20:54 UTC  
> Updated_at: 2017-02-03 00:47:32 UTC  
> Url: https://github.com/boostorg/config/pull/110#issuecomment-277128031  

> Boost built with MS VS 2017 RC has -vc150 suffix.  
  
I think libraries should be named with suffix `-vc140` because VS2017 fully backward compatible with VS2015.  
  
I think it should be `-vc140`, because we can't specify `#pragma comment lib` to load `-vc140` or `-vc150` library  
  
UPDATE: IDK now after this S.T.L. comment https://www.reddit.com/r/cpp/comments/5reyml/notes_on_visual_studio_15/dd6x3b7/  
  
At least we should support some define to use Boost  `-vc140` with VS2017

---

## Comment 7

> Username: KindDragon  
> Created_at: 2017-02-03 00:22:56 UTC  
> Updated_at: 2017-02-03 00:23:06 UTC  
> Url: https://github.com/boostorg/config/pull/110#issuecomment-277128379  

> Then maybe we should just wait until VS 2017 is officially released instead of hacking away at it and not really knowing what it will be until it is released.  
  
Microsoft already release VS2017RC, so nothing will change

---

## Comment 8

> Username: KindDragon  
> Created_at: 2017-02-03 00:28:48 UTC  
> Url: https://github.com/boostorg/config/pull/110#issuecomment-277129418  

> Do we know if Microsoft are calling it VC15 or VC14.1?  
  
Visual Studio version will be 15 (https://visualstudio.uservoice.com/forums/121579-visual-studio-ide/suggestions/13536840-adopt-a-simple-and-obvious-versioning-scheme-for-t), but CRT version will be 14.1 (https://build2.org/article/notes-visual-studio-15.xhtml)

---

## Comment 9

> Username: o01eg  
> Created_at: 2017-02-03 03:51:00 UTC  
> Url: https://github.com/boostorg/config/pull/110#issuecomment-277158124  

Boost build already generates vc150 suffix.

---

## Comment 10

> Username: OlafvdSpek  
> Created_at: 2017-02-03 09:34:01 UTC  
> Updated_at: 2017-02-03 09:36:35 UTC  
> Url: https://github.com/boostorg/config/pull/110#issuecomment-277203493  

@KindDragon   
> I think libraries should be named with suffix -vc140 because VS2017 fully backward compatible with VS2015.  
  
Does vc140 support std::string_view too?  
vc141 does..  
  
@eldiener The third RC was released last week, I don't think MS is going to make major changes, therefore it doesn't make sense to wait.

---

## Comment 11

> Username: KindDragon  
> Created_at: 2017-02-03 10:36:19 UTC  
> Updated_at: 2017-02-03 10:41:09 UTC  
> Url: https://github.com/boostorg/config/pull/110#issuecomment-277216210  

> Boost build already generates vc150 suffix.  
  
Boost Build can't compile Boost with VS2017 in the current state.

---

## Comment 12

> Username: o01eg  
> Created_at: 2017-02-03 11:22:46 UTC  
> Url: https://github.com/boostorg/config/pull/110#issuecomment-277224950  

@KindDragon It has initial support and with some patches I built it: https://github.com/o01eg/freeorion-sdk

---

## Comment 13

> Username: KindDragon  
> Created_at: 2017-02-03 11:44:38 UTC  
> Url: https://github.com/boostorg/config/pull/110#issuecomment-277228658  

@o01eg Yes it possible, but in general user wouldn't fo it. So we can change suffix if need it

---

## Comment 14

> Username: jzmaddock  
> Created_at: 2017-02-04 13:22:11 UTC  
> Url: https://github.com/boostorg/config/pull/110#issuecomment-277445588  

OK let's try this for now, and change later if needed.

---

## Comment 15

> Username: KindDragon  
> Created_at: 2017-02-04 15:00:21 UTC  
> Url: https://github.com/boostorg/config/pull/110#issuecomment-277451269  

So user can define `BOOST_LIB_TOOLSET ="vc140"` to use `vc140` libraries with VS2017?

---

## Comment 16

> Username: jzmaddock  
> Created_at: 2017-02-04 15:42:45 UTC  
> Url: https://github.com/boostorg/config/pull/110#issuecomment-277453940  

> So user can define |BOOST_LIB_TOOLSET ="vc140"| to use |vc140|   
> libraries with VS2017?  
>  
  
Yes.

---
