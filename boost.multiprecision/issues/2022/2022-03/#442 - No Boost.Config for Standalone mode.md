# #442 - No Boost.Config for Standalone mode [Closed]

> Username: c-lipka  
> Created at: 2022-03-20 09:35:47 UTC  
> Updated at: 2022-08-31 17:45:03 UTC  
> Closed at: 2022-08-31 17:10:05 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/442  

Despite the `readme.md` in version [boost-1.79.0.beta1](https://github.com/boostorg/multiprecision/releases/tag/boost-1.79.0.beta1) claiming that "Our package on this page already includes a copy of Boost.Config", that does not seem to be the case; at any rate, I can't find any sign of it in the zip archive downloaded from GitHub (or in the current master branch, for that matter).  
  
If this is intentional, it would be helpful to rephrase the `readme.md`.

---

## Comment 1

> Username: mborland  
> Created at: 2022-04-16 00:18:26 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/442#issuecomment-1100480424  

@NAThompson, @ckormanyos, @jzmaddock Now that 1.79 is on the streets can you cut a release that bundles standalone with config?

---

## Comment 2

> Username: jzmaddock  
> Created at: 2022-04-16 12:20:28 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/442#issuecomment-1100651867  

Is there an "easy" way to do this that doesn't involve manually adding the libs/config/include files on a branch and then releasing that?  Or maybe that *is* the easy way!?  Otherwise we may need to come up with a script or something to simplify things...

---

## Comment 3

> Username: mborland  
> Created at: 2022-04-16 21:37:24 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/442#issuecomment-1100758534  

I just cut the release for Boost.Math standalone 1.79. It looks like you'll have to manually add the files to the release. You could also try adding Boost.Config as a submodule to this one but I feel like that will cause other headaches.

---

## Comment 4

> Username: pdeitel  
> Created at: 2022-08-31 16:18:45 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/442#issuecomment-1233151137  

Any updates on including boost/config.hpp in this standalone multiprecision instance?

---

## Comment 5

> Username: jzmaddock  
> Created at: 2022-08-31 17:07:05 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/442#issuecomment-1233199083  

If you download the "official" release files then they do have Boost.Config included.  However, if you just grab the source then you will need to get Config separately.

---

## Comment 6

> Username: jzmaddock  
> Created at: 2022-08-31 17:10:04 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/442#issuecomment-1233202237  

Which is to say this issue should be regarded as fixed by https://github.com/boostorg/multiprecision/releases.  
  
I'll double check the readme file as well.

---

## Comment 7

> Username: pdeitel  
> Created at: 2022-08-31 17:10:23 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/442#issuecomment-1233202537  

Is the official release a separate repository?  
________________________________  
From: jzmaddock ***@***.***>  
Sent: Wednesday, August 31, 2022 1:07:16 PM  
To: boostorg/multiprecision ***@***.***>  
Cc: Paul Deitel ***@***.***>; Comment ***@***.***>  
Subject: Re: [boostorg/multiprecision] No Boost.Config for Standalone mode (Issue #442)  
  
  
If you download the "official" release files then they do have Boost.Config included. However, if you just grab the source then you will need to get Config separately.  
  
—  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/multiprecision/issues/442#issuecomment-1233199083>, or unsubscribe<https://github.com/notifications/unsubscribe-auth/ABCWXNL6A4QN3FFJUB2RIJLV36GMJANCNFSM5RFIHFPQ>.  
You are receiving this because you commented.Message ID: ***@***.***>

---

## Comment 8

> Username: pdeitel  
> Created at: 2022-08-31 17:45:02 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/442#issuecomment-1233235119  

﻿Thank you!  
  
From: jzmaddock ***@***.***>  
Date: Wednesday, August 31, 2022 at 1:10 PM  
To: boostorg/multiprecision ***@***.***>  
Cc: Paul Deitel ***@***.***>, Comment ***@***.***>  
Subject: Re: [boostorg/multiprecision] No Boost.Config for Standalone mode (Issue #442)  
  
Which is to say this issue should be regarded as fixed by https://github.com/boostorg/multiprecision/releases.  
  
I'll double check the readme file as well.  
  
—  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/multiprecision/issues/442#issuecomment-1233202237>, or unsubscribe<https://github.com/notifications/unsubscribe-auth/ABCWXNJ2VNARKZSBNRLYRKLV36GXRANCNFSM5RFIHFPQ>.  
You are receiving this because you commented.Message ID: ***@***.***>
