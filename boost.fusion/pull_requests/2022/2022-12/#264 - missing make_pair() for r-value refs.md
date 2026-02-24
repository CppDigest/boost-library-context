# #264 missing make_pair() for r-value refs [Merged]

> Username: niXman  
> Created at: 2022-12-21 14:05:37 UTC  
> Updated at: 2023-02-11 07:31:51 UTC  
> Merged at: 2023-02-11 02:10:26 UTC  
> Closed at: 2023-02-11 02:10:26 UTC  
> Url: https://github.com/boostorg/fusion/pull/264  

hello guys!  
  
the `make_pair()` for r-value refs is required for r-value, otherwise `non-copyable` but `movable` can't be constructed.  
  
  
thanks!

---

## Comment 1

> Username: niXman  
> Created_at: 2022-12-22 07:32:22 UTC  
> Url: https://github.com/boostorg/fusion/pull/264#issuecomment-1362506715  

@here could anyone approve this PR please?

---

## Comment 2

> Username: daminetreg  
> Created_at: 2022-12-22 14:01:16 UTC  
> Url: https://github.com/boostorg/fusion/pull/264#issuecomment-1362873231  

Could you add a test case present an example of the problem with an example user code of the fix ?

---

## Comment 3

> Username: niXman  
> Created_at: 2022-12-22 14:14:16 UTC  
> Updated_at: 2022-12-22 14:14:44 UTC  
> Url: https://github.com/boostorg/fusion/pull/264#issuecomment-1362888474  

@daminetreg I can if you provide me with a link to the howto/tutorial =)

---

## Comment 4

> Username: daminetreg  
> Created_at: 2022-12-22 14:34:02 UTC  
> Url: https://github.com/boostorg/fusion/pull/264#issuecomment-1362910565  

> @daminetreg I can if you provide me with a link to the howto/tutorial =)  
  
I merely think adding a small test case representing the case you fixed around here : https://github.com/boostorg/fusion/blob/38060fab3a6afbdecac7525c6b970e05ff4d3128/test/sequence/map.cpp#L154  
  
would probably help to understand the problem you fix and check there are no future regression on it.  
  
It is possibly a more general problem with all the Boost.Fusion containers and should the fix also be made to all result_of protocols ? What I'm unsure though is if there is not generally something that could be simplified about result_of generally since the inception of decltype and so on.

---

## Comment 5

> Username: daminetreg  
> Created_at: 2022-12-22 14:34:45 UTC  
> Url: https://github.com/boostorg/fusion/pull/264#issuecomment-1362911291  

And in any case thank you so much for helping improve Boost.Fusion that we all depend on. 😄

---

## Comment 6

> Username: niXman  
> Created_at: 2022-12-22 14:37:56 UTC  
> Updated_at: 2022-12-22 19:56:02 UTC  
> Url: https://github.com/boostorg/fusion/pull/264#issuecomment-1362914628  

> I merely think adding a small test case representing the case you fixed around here :  
> ...  
> would probably help to understand the problem you fix and check there are no future regression on it.  
  
got it, will do now.  
  
> It is possibly a more general problem with all the Boost.Fusion containers and should the fix also be made to all result_of protocols ?  
  
as far as I understand `result_of protocols`, the fix doesn't apply to that...

---

## Comment 7

> Username: niXman  
> Created_at: 2022-12-22 20:41:53 UTC  
> Url: https://github.com/boostorg/fusion/pull/264#issuecomment-1363323839  

@daminetreg done!  
could you help with approving this PR please?

---

## Comment 8

> Username: niXman  
> Created_at: 2022-12-22 20:51:29 UTC  
> Url: https://github.com/boostorg/fusion/pull/264#issuecomment-1363329755  

oh %)  
  
according the the report: https://ci.appveyor.com/project/Flast/fusion/builds/45756532/job/uyu4sjb30msjvmyj  
there is smth wrong with `b2` or with Jamfile... I've no ideas what should I do next...  
```  
b2 -j%NUMBER_OF_PROCESSORS% --hash libs/fusion/test toolset=%TOOLSET% cxxstd=%CXXSTD% %ADDRMD%  
Performing configuration checks  
    - default address-model    : 64-bit [1]  
    - default architecture     : x86 [1]  
    - symlinks supported       : yes (cached)  
    - cxx11_rvalue_references  : yes [2]  
    - cxx11_variadic_templates : yes [2]  
    - cxx11_hdr_tuple          : yes [2]  
    - cxx11_hdr_array          : yes [2]  
error: No best alternative for libs/fusion/test/make_pair_r-value  
    next alternative: required properties: (empty)  
        matched  
    next alternative: required properties: (empty)  
        matched  
error: No best alternative for libs/fusion/test/make_pair_r-value  
    next alternative: required properties: (empty)  
        matched  
    next alternative: required properties: (empty)  
        matched  
  
```

---

## Comment 9

> Username: djowel  
> Created_at: 2022-12-23 00:42:15 UTC  
> Url: https://github.com/boostorg/fusion/pull/264#issuecomment-1363474816  

Looks good to me.

---

## Comment 10

> Username: niXman  
> Created_at: 2022-12-29 20:23:00 UTC  
> Url: https://github.com/boostorg/fusion/pull/264#issuecomment-1367568777  

@djowel so can it be merged? :)

---

## Comment 11

> Username: niXman  
> Created_at: 2023-02-09 15:43:14 UTC  
> Updated_at: 2023-02-09 15:44:06 UTC  
> Url: https://github.com/boostorg/fusion/pull/264#issuecomment-1424402890  

@daminetreg  @djowel it there any reason to not merge it? could you please explain?

---

## Comment 12

> Username: djowel  
> Created_at: 2023-02-11 02:10:50 UTC  
> Url: https://github.com/boostorg/fusion/pull/264#issuecomment-1426573454  

Merged.

---

## Comment 13

> Username: niXman  
> Created_at: 2023-02-11 07:31:51 UTC  
> Url: https://github.com/boostorg/fusion/pull/264#issuecomment-1426647342  

thank you!

---
