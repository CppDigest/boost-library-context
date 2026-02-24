# #780 - Review integration modes in docs [Closed]

> Username: alandefreitas  
> Created at: 2023-10-04 17:48:12 UTC  
> Updated at: 2023-10-13 21:40:37 UTC  
> Closed at: 2023-10-13 21:40:37 UTC  
> Url: https://github.com/boostorg/url/issues/780  

The docs should be updated to reflect the integration options. In particular, the "header-only" mode has been ~deprecated~ discontinued.

---

## Comment 1

> Username: mdmillerii  
> Created at: 2023-10-04 19:12:38 UTC  
> Url: https://github.com/boostorg/url/issues/780#issuecomment-1747486990  

Deprecated would imply it could be used but is discouraged for a new method or replacement.   The feature was ["discontinued"](https://github.com/boostorg/url/blame/1dc789839f34f9a13451c3aaa5bf4cacec04fd78/include/boost/url/src.hpp#L16) and effectively disabled, although I don't know where or how the predecessor method was implemented (I'm assuming this issue will discuss the intended replacement).  
  
Also where is the overview maintained?  I see header-only it was removed from the 1.83 release notes [website](https://www.boost.org/doc/libs/1_83_0/libs/url/doc/html/url/overview.html)  but is still in the [adoc file](https://github.com/boostorg/url/blob/1dc789839f34f9a13451c3aaa5bf4cacec04fd78/doc/library-detail.adoc?plain=1#L109  
) merged in the repository (and is still a feature of the prior 1.82 release).  
  
  
https://www.merriam-webster.com/dictionary/deprecate (`3`)  
  
https://www.oxfordlearnersdictionaries.com/definition/english/deprecate (`2`)

---

## Comment 2

> Username: alandefreitas  
> Created at: 2023-10-04 19:24:24 UTC  
> Url: https://github.com/boostorg/url/issues/780#issuecomment-1747503046  

Oh. That's correct.

---

## Comment 3

> Username: alandefreitas  
> Created at: 2023-10-04 19:27:57 UTC  
> Url: https://github.com/boostorg/url/issues/780#issuecomment-1747507836  

The documentation _was_ properly updated when 963bf5a4 was merged. I made an error.

---

## Comment 4

> Username: edtanous  
> Created at: 2023-10-04 20:44:35 UTC  
> Url: https://github.com/boostorg/url/issues/780#issuecomment-1747612951  

https://github.com/boostorg/url/blob/develop/README.md#header-only  
  
develop branch still shows the docs, as does the boost-1.83.0 branch.  Where were the docs updated?

---

## Comment 5

> Username: alandefreitas  
> Created at: 2023-10-04 22:50:33 UTC  
> Updated at: 2023-10-04 22:50:41 UTC  
> Url: https://github.com/boostorg/url/issues/780#issuecomment-1747753883  

Right. Yes. We left the README.md out. (These are not [_the_ docs](https://www.boost.org/doc/libs/master/libs/url/doc/html/index.html) though.)

---

## Comment 6

> Username: lava  
> Created at: 2023-10-09 10:13:47 UTC  
> Url: https://github.com/boostorg/url/issues/780#issuecomment-1752728007  

The README on master also still mentions  
  
> Optional header-only, without linking to a library  
  
as one of the design goals of the library, which probably should be updated as well.
