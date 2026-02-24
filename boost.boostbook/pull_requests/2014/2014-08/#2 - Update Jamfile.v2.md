# #2 Update Jamfile.v2 [Closed]

> Username: jzmaddock  
> Created at: 2014-08-13 17:51:23 UTC  
> Updated at: 2014-08-14 08:26:24 UTC  
> Closed at: 2014-08-13 19:04:07 UTC  
> Url: https://github.com/boostorg/boostbook/pull/2  

There can be only one project named boost/doc and we have that already under /doc/  
Change to something unique.  
Fixes PDF doc build.

---

## Comment 1

> Username: danieljames  
> Created_at: 2014-08-13 19:04:07 UTC  
> Url: https://github.com/boostorg/boostbook/pull/2#issuecomment-52094814  

What's with the duplicate pull requests?

---

## Comment 2

> Username: ericniebler  
> Created_at: 2014-08-13 23:26:03 UTC  
> Url: https://github.com/boostorg/boostbook/pull/2#issuecomment-52125526  

Looks like John made separate pull requests for the master and develop branches. It's generally better to merge into develop, and then merge that into master. That way, you also merge into master the merge commit. So one pull request into develop should do (assuming develop can be safely merged into master, which _should_ be true).

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2014-08-14 08:26:24 UTC  
> Url: https://github.com/boostorg/boostbook/pull/2#issuecomment-52156265  

Correct, I made one for master and one for develop - basically because I really don't want to have to go through this patching process again next release.  However, I don't mind how you merge them as long as the patch goes into master ;-)

---
