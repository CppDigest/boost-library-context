# #110 Fixed issues with linking with external 3rd parties on Windows [Closed]

> Username: ColinChargyBentley  
> Created at: 2019-07-25 09:06:10 UTC  
> Updated at: 2021-06-15 16:48:21 UTC  
> Closed at: 2021-06-15 16:48:21 UTC  
> Url: https://github.com/boostorg/uuid/pull/110  

Hi,  
Currently, when enabling BOOST_AUTO_LINK_TAGGED or BOOST_AUTO_LINK_SYSTEM, the build with boost fails because auto_link is used for non boost libraries (Windows libraries) : bcrypt.lib, coredll.lib and advapi32.lib. Boost auto_link seems to be restricted to boost libraries as it constructs library name depending on Boost options. Since these Windows libs does not follow boost naming convention so auto_link shouldn't be used for thoses.  
  
This has already been discussed : https://github.com/boostorg/uuid/pull/95#issuecomment-514135091and https://github.com/boostorg/uuid/issues/94.  
  
Regards,  
Colin Chargy

---

## Review 1 [Changes requested]

> Username: jeking3  
> Created_at: 2019-07-27 20:47:56 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/uuid/pull/110#pullrequestreview-267481849  

It didn't seem to work for clang-cl, an option in Visual Studio.  See failed CI build.  I assume you found this logic somewhere else, given it has Metrowerks in it?

---

## Comment 2

> Username: ColinChargyBentley  
> Created_at: 2019-07-29 07:26:57 UTC  
> Url: https://github.com/boostorg/uuid/pull/110#issuecomment-515877620  

Hi @jeking3 ,  
I found this in boost auto_link : https://github.com/boostorg/config/blob/f7b608155b3396eb33fe9c800f9090f979f0132e/include/boost/config/auto_link.hpp#L99 . I would like to keep the behavior as close as possible to the previous one (in the case where it worked).  
  
I assumed that the comment in https://github.com/boostorg/config/blob/f7b608155b3396eb33fe9c800f9090f979f0132e/include/boost/config/auto_link.hpp#L450 was wrong because it refers to the #ifdef I copied.  
  
What do you think ? Can you see where the behavior changes ?  
  
Regards,  
Colin Chargy

---

## Comment 3

> Username: MarcelRaad  
> Created_at: 2020-03-05 12:35:59 UTC  
> Url: https://github.com/boostorg/uuid/pull/110#issuecomment-595206990  

Is anyone still working on this or should I create a new PR? It's only this line that's missing:  
https://github.com/boostorg/config/blob/a18911902dff03f37582696a13bf60768c183c55/include/boost/config/auto_link.hpp#L103

---

## Comment 4

> Username: ColinChargyBentley  
> Created_at: 2020-03-05 12:44:11 UTC  
> Url: https://github.com/boostorg/uuid/pull/110#issuecomment-595209888  

@MarcelRaad You should create a new PR!

---

## Comment 5

> Username: MarcelRaad  
> Created_at: 2020-03-05 12:46:13 UTC  
> Url: https://github.com/boostorg/uuid/pull/110#issuecomment-595210600  

Will do, thanks!

---

## Comment 6

> Username: mclow  
> Created_at: 2020-07-24 20:15:08 UTC  
> Url: https://github.com/boostorg/uuid/pull/110#issuecomment-663715417  

Should this PR be closed, then?

---

## Comment 7

> Username: MarcelRaad  
> Created_at: 2020-07-24 23:04:10 UTC  
> Updated_at: 2020-07-25 06:56:16 UTC  
> Url: https://github.com/boostorg/uuid/pull/110#issuecomment-663766999  

Yes, as https://github.com/boostorg/config/commit/68b8da3446df6984b74b1bdf6f458d1425498d29 is in master now, I don't think this is needed anymore.

---
