# #471 Finish remaining loose ends/docs for 1-dot-80 [Merged]

> Username: ckormanyos  
> Created at: 2022-06-07 09:47:23 UTC  
> Updated at: 2022-06-19 08:56:36 UTC  
> Merged at: 2022-06-18 15:18:42 UTC  
> Closed at: 2022-06-18 15:18:42 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/471  

The purpose of this PR is to finish up loose ends moving forward to 1.80. The notes on performance have been included in qbk related to #443 and #444.  
  
Also the last review comment from #470 regarding an unused variable is handled.

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2022-06-08 05:16:35 UTC  
> Updated_at: 2022-06-08 05:19:09 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/471#issuecomment-1149472267  

Hi @jzmaddock the added text for notes on performance is present as a draft.  
  
For a while. I've always had trouble building the docs. This is a bit of a knowledge-gap on my side, and I'd like to learn how to build and deploy the docs. Is there some rudimentary info on this?

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2022-06-08 17:48:48 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/471#issuecomment-1150215412  

Rather outdated, but still covers the basics: https://svn.boost.org/trac10/wiki/BoostDocs/GettingStarted  
  
For Math/Multiprecision docs, you only need xsltproc, plus the xslt stylesheets, and the Docbook DTD.  Pre-building quickbook speeds up building but is optional.  
  
The end of your user-config.jam should look something like:  
  
```  
# Only need the path argument if the executable isn't in your path:  
using xsltproc  
    : "d:/download/open/xsltproc/bin/xsltproc.exe"  
    ;  
  
# Paths to the stylesheets and 4.2 DTD, this is required, and the DTD version must be 4.2 and nothing else:  
using boostbook  
    : "D:/download/open/docbook/docbook-xsl-1.79.1"  
    : "D:/download/open/docbook/docbook-dtd-4.2"  
    ;  
  
# Optional, if these executables are pre-built for faster build times:  
using quickbook : d:/data/boost/boost/dist/bin/quickbook.exe ;  
using auto-index : d:/data/boost/boost/dist/bin/auto_index.exe ;  
```

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2022-06-11 12:49:54 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/471#issuecomment-1152922309  

See also issue #473

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2022-06-18 15:17:13 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/471#issuecomment-1159482039  

>> always had trouble building the docs  
  
> but still covers the basics  
  
Well, I tried a few more times, but was unable to get it working. I'll take another crack at building the docs at a future time.  
  
I got `...found 2 targets`, but I think it's so rudimentary, i don't know the actual name of the bjam target needed to be built in the Jamfile. Oh well. Another time.  
  
For now, I've merged this PR, as it contains a few fixes relevant for 1.80, in the hopes that I can learn how to build the docs in the future. So there are some new paragraphs for the docs, but these have not yet actually been built. Sorry for that.

---
