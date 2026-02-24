# #19 Upgrade DOCBOOK_XSL_VERSION [Closed]

> Username: sdarwin  
> Created at: 2023-02-17 21:37:23 UTC  
> Updated at: 2024-09-25 22:28:55 UTC  
> Closed at: 2024-09-25 22:28:55 UTC  
> Url: https://github.com/boostorg/boostbook/pull/19  

Per a request, this updates DOCBOOK_XSL_VERSION from 1.75.2 to 1.79.1, and switches to the zip file download because the tar.gz archive isn't available.  
  
As far as the general topic of installing the docs toolchain, these scripts at https://github.com/boostorg/release-tools/tree/develop/build_docs are able to install necessary apt, gem, and pip packages, and build docs for boost libraries.  So it's probably not necessary to extensively enhance `setup_boostbook.sh`, just use the `build_docs` scripts.

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2023-02-26 14:27:56 UTC  
> Url: https://github.com/boostorg/boostbook/pull/19#issuecomment-1445375176  

build_docs is interesting, didn't know about that.

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2023-04-24 21:44:06 UTC  
> Url: https://github.com/boostorg/boostbook/pull/19#issuecomment-1520869671  

Someone got errors building the docs for Boost.Core, and this pull request would fix it.

---

## Comment 3

> Username: Lastique  
> Created_at: 2024-05-05 12:03:58 UTC  
> Updated_at: 2024-05-05 12:04:28 UTC  
> Url: https://github.com/boostorg/boostbook/pull/19#issuecomment-2094779750  

> and switches to the zip file download because the tar.gz archive isn't available.  
  
There is a `.tar.bz2` archive.

---

## Comment 4

> Username: Lastique  
> Created_at: 2024-05-05 12:11:04 UTC  
> Url: https://github.com/boostorg/boostbook/pull/19#issuecomment-2094782745  

And by the way, the project moved to GitHub, and there already was a 1.79.2 release:  
  
https://github.com/docbook/xslt10-stylesheets/releases/tag/release%2F1.79.2

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2024-05-06 16:47:15 UTC  
> Url: https://github.com/boostorg/boostbook/pull/19#issuecomment-2096481388  

Anyway... since it has been over a year with no progress merging this, I think it is safe to close it. Note that we are moving away from the Quickbook toolchain because it is not being actively maintained and developed. Instead we are focusing on Asciidoctor.

---

## Comment 6

> Username: Lastique  
> Created_at: 2024-05-06 16:56:18 UTC  
> Url: https://github.com/boostorg/boostbook/pull/19#issuecomment-2096496970  

I'm certainly not moving away from QuickBook.

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2024-05-06 17:36:50 UTC  
> Url: https://github.com/boostorg/boostbook/pull/19#issuecomment-2096567950  

> I'm certainly not moving away from QuickBook.  
  
No one is asking you to. The "we" in my statement above refers to myself, Peter Dimov, Glen Fernandes, Rene Morell, all C++ Alliance Staff Engineers, and any other library author or maintainers who want to update to a modern, well-supported toolchain.

---

## Review 8 [Commented]

> Username: barendgehrels  
> Created_at: 2024-09-22 18:03:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/boostbook/pull/19#pullrequestreview-2320897737  

📁 setup_boostbook.py

```diff
  13 | # (MAINTANERS: please, keep in synch with setup_boostbook.sh)
  14 |- DOCBOOK_XSL_VERSION = "1.75.2"
  14 |+ DOCBOOK_XSL_VERSION = "1.79.1"
```

> Username: barendgehrels  
> Created_at: 2024-09-22 18:03:37 UTC  
> Updated_at: 2024-09-22 18:04:47 UTC  
> Url: https://github.com/boostorg/boostbook/pull/19#discussion_r1770604934  

Version `1.79.2` (the newest) gives errors in the chunking. The documentation is not correct anymore with the parameters supplied in current Jamfiles (at least, not for Boost.Geometry). It misses all tables of contents (apart from the one on the main page). Effectively it's completely broken.  
  
That's likely the case with `1.79.1` as well, given that it is one patch lower.

> Username: barendgehrels  
> Created_at: 2024-09-23 16:22:48 UTC  
> Updated_at: 2024-09-23 16:22:49 UTC  
> Url: https://github.com/boostorg/boostbook/pull/19#discussion_r1771753302  

With version `1.79.1` , generation of Boost.Geometry is correct!


---
