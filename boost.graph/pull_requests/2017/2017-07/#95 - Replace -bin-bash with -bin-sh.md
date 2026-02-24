# #95 Replace /bin/bash with /bin/sh [Merged]

> Username: asomers  
> Created at: 2017-07-27 20:34:47 UTC  
> Updated at: 2018-10-12 17:32:22 UTC  
> Merged at: 2018-10-12 17:32:22 UTC  
> Closed at: 2018-10-12 17:32:22 UTC  
> Url: https://github.com/boostorg/graph/pull/95  

/bin/bash is a Linuxism.  /bin/sh is portable, and this script isn't  
using any bash-specific features.

---

## Comment 1

> Username: anadon  
> Created_at: 2018-08-31 17:49:42 UTC  
> Url: https://github.com/boostorg/graph/pull/95#issuecomment-417741579  

I'm helping out with the PR backlog. BSD, Linux, Mac, and now Windows have bash support.  Why is this change needed?  This will be in the first batch of merged PR's. This is to let you know and help me prioritize PR's.

---

## Comment 2

> Username: asomers  
> Created_at: 2018-08-31 17:51:16 UTC  
> Url: https://github.com/boostorg/graph/pull/95#issuecomment-417741990  

Because while bash _can_ run almost everywhere, it isn't necessarily installed by default, and on many systems /bin/bash isn't the correct path.  /bin/sh is correct on every unix system.

---

## Comment 3

> Username: anadon  
> Created_at: 2018-08-31 18:44:49 UTC  
> Url: https://github.com/boostorg/graph/pull/95#issuecomment-417756673  

Could you provide an example?  I'd like a concrete need in order to immediately accept this.

---

## Comment 4

> Username: asomers  
> Created_at: 2018-08-31 19:12:04 UTC  
> Url: https://github.com/boostorg/graph/pull/95#issuecomment-417763400  

On the BSDs, bash is usually installed to /usr/local/bin/bash, and is not installed by default.  On OSX, it can vary based on whether you're using homebrew, macports, etc.  I've even heard of Linux distros that don't use /bin/bash, but I can't find any on Google right now.  Some programs use "#! /usr/bin/env bash", which is more portable.  But it's always better to use "#! /bin/sh" for scripts that are bourne-compatible, which this one is.

---

## Comment 5

> Username: anadon  
> Created_at: 2018-08-31 19:13:26 UTC  
> Url: https://github.com/boostorg/graph/pull/95#issuecomment-417763743  

Good enough for me.  I'll add this to the first batch of merges since it can fix things.

---
