# #29 - boostbook.html not found [Closed]

> Username: sdarwin  
> Created at: 2024-08-15 11:26:55 UTC  
> Updated at: 2024-12-27 12:54:46 UTC  
> Closed at: 2024-12-27 12:54:44 UTC  
> Url: https://github.com/boostorg/boostbook/issues/29  

Starting in boost 1.86.0 the boostbook documentation is missing.  
  
In boost 1.85.0:  
  
doc/html/boostbook.html - initial page of documentation  
doc/html/boostbook/ - folder with documentation  
  
In boost 1.86.0:  
  
doc/html/boostbook.html - redirect page  
doc/html/boostbook/ - folder missing  
  
  
This could be caused by modifications or updates in:    
1. boostbook   
2. b2   
3. release-tools scripts  
4. release-tools build container  
5. other..  
  
I have just tried building boost with the earlier release-tools image and scripts to attempt to eliminate that factor. The problem seems to still be present, so that is some evidence 3 or 4 are not the reason.   
  
@Lastique , there were updates in this boostorg/boostbook repo. Any ideas?

---

## Comment 1

> Username: Lastique  
> Created at: 2024-08-15 12:00:21 UTC  
> Url: https://github.com/boostorg/boostbook/issues/29#issuecomment-2291142977  

`doc/html/boostbook.html` is being built from `doc/boostbook.xml` using `b2`. Previously, a generated version of html was committed in git, in 1.86 it was removed. The superproject was [updated](https://github.com/boostorg/boost/commit/2403dacaa7c37e8207180a5b5e19ef2b062e21ea) accordingly, and should build html documentation as part of the docs build process. At least, that was the expectation.

---

## Comment 2

> Username: Lastique  
> Created at: 2024-08-15 12:03:26 UTC  
> Url: https://github.com/boostorg/boostbook/issues/29#issuecomment-2291146943  

I think I see the problem. The built documentation is not `doc/html/boostbook.html`, it's `doc/html/index.html`. There's incorrect redirection in `tools/boostbook/index.html`, which I'll fix now.

---

## Comment 3

> Username: sdarwin  
> Created at: 2024-08-15 12:08:03 UTC  
> Url: https://github.com/boostorg/boostbook/issues/29#issuecomment-2291152795  

Ok. For testing, here are instructions to build the superproject locally:  https://github.com/boostorg/release-tools/tree/develop/docs    
Or if you check in a fix, the daily snapshots will contain the changes: https://archives.boost.io/develop/

---

## Comment 4

> Username: Lastique  
> Created at: 2024-08-15 12:13:17 UTC  
> Url: https://github.com/boostorg/boostbook/issues/29#issuecomment-2291159513  

> I think I see the problem. The built documentation is not `doc/html/boostbook.html`, it's `doc/html/index.html`. There's incorrect redirection in `tools/boostbook/index.html`, which I'll fix now.  
  
I spoke too soon. In `tools/boostbook/index.html` I see redirection to `../../doc/html/boostbook.html` (which I initially misread as `doc/html/boostbook.html`). The `../../doc/html/boostbook.html` [does exist](https://github.com/boostorg/boost/blob/develop/doc/html/boostbook.html) in the superproject and redirects to `http://www.boost.org/doc/libs/master/doc/html/boostbook.html`, which is the same file but on master. And master contains the same file again, so it redirects to itself. I'm confused how this is supposed to work.

---

## Comment 5

> Username: sdarwin  
> Created at: 2024-08-15 12:15:10 UTC  
> Url: https://github.com/boostorg/boostbook/issues/29#issuecomment-2291161901  

> And master contains the same file again, so it redirects to itself.  
  
Right.  It makes no sense.  :-)   Maybe the superproject build is supposed to overwrite that circular dependency, replacing the files.

---

## Comment 6

> Username: Lastique  
> Created at: 2024-08-15 12:37:33 UTC  
> Url: https://github.com/boostorg/boostbook/issues/29#issuecomment-2291191164  

> Maybe the superproject build is supposed to overwrite that circular dependency, replacing the files.  
  
I can't find anything to that effect in the superproject or release-tools, though I don't have a deep knowledge of either internals.  
  
For now I changed `tools/boostbook/index.html` to redirect to `doc/html/index.html`, which is the generated BoostBook docs.

---

## Comment 7

> Username: Lastique  
> Created at: 2024-12-27 12:54:44 UTC  
> Url: https://github.com/boostorg/boostbook/issues/29#issuecomment-2563675986  

In Boost 1.87.0 BoostBook documentation is reachable from the [website](https://www.boost.org/doc/libs/1_87_0/).
