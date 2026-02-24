# #6 Fix nits with linux-user-config.jam [Closed]

> Username: pabigot  
> Created at: 2013-12-26 13:55:46 UTC  
> Updated at: 2014-02-24 12:31:42 UTC  
> Closed at: 2014-02-24 12:31:41 UTC  
> Url: https://github.com/boostorg/boost/pull/6  

The python issue bit me; the misspelled package is a drive-by.

---

## Comment 1

> Username: vprus  
> Created_at: 2014-01-22 12:27:57 UTC  
> Url: https://github.com/boostorg/boost/pull/6#issuecomment-33017232  

Thanks for the patches! I must admit I forgot what linux-user-config.jam is, and when it's used. Can you remind me please so that I can evaluate the change?

---

## Comment 2

> Username: pabigot  
> Created_at: 2014-01-22 13:04:22 UTC  
> Url: https://github.com/boostorg/boost/pull/6#issuecomment-33019616  

AFAICT it's an example user-config.jam targeted to Linux users.  Without some of the configuration commands in it, you can't build documentation.

---

## Comment 3

> Username: vprus  
> Created_at: 2014-01-22 13:18:20 UTC  
> Url: https://github.com/boostorg/boost/pull/6#issuecomment-33020462  

Actually, it looks like the file was added by Beman, and I'm not sure ordinary users are supposed to use it. You probably check with him.

---

## Comment 4

> Username: pabigot  
> Created_at: 2014-01-22 13:38:39 UTC  
> Url: https://github.com/boostorg/boost/pull/6#issuecomment-33022149  

I don't particularly care who reviews it, or even whether it gets merged.  New developers cannot build documentation without adding boostbook rules to some jam file; the standard location appears to be user-config.jam, and this is the only file that has the right directives.  There's some contemporaneous email on the developers list in my "how do I do this" query which confirmed this is the right approach.  
  
I thought I'd help out and try to make it a bit easier for new developers by supplying a patch so when people do use this it doesn't break something else, but I'm certainly not going to go around trying to identify a maintainer who feels qualified to review the patch.  If you're not comfortable with what it does, and nobody else is maintaining this module, just drop it.

---

## Comment 5

> Username: danieljames  
> Created_at: 2014-02-24 12:31:40 UTC  
> Url: https://github.com/boostorg/boost/pull/6#issuecomment-35881534  

This file is no longer part of the boost tree as the release directory has been removed, so I'm closing this pull request.

---
