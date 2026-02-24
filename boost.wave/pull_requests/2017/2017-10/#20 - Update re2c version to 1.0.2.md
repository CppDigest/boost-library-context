# #20 Update re2c version to 1.0.2 [Merged]

> Username: jefftrull  
> Created at: 2017-10-24 17:30:57 UTC  
> Updated at: 2017-10-25 05:15:15 UTC  
> Merged at: 2017-10-25 00:44:28 UTC  
> Closed at: 2017-10-25 00:44:28 UTC  
> Url: https://github.com/boostorg/wave/pull/20  

In the 9 years since 0.13.5, upon which Wave is currently based, was released many bugfixes and improvements have been implemented.  The current maintainer, active since 2014, has really pushed it forward and I think Wave could benefit greatly from the improvements.  In particular:  
  
1. A smaller DFA is now produced (1098 states vs. 1222)  
2. The "trailing context" (helpful for distinguishing between keywords and identifiers) feature was fixed  
3. A powerful new feature, _submatch extraction_, was introduced  
4. The "generic input" API was introduced, permitting proper support of forward iterators.  This permits fixing [TRAC 12208](https://svn.boost.org/trac10/ticket/12208)  
  
More generally the richer feature set will make it easier to resolve the problems with string literals I noted in TRAC [12602](https://svn.boost.org/trac10/ticket/12602) and [12603](https://svn.boost.org/trac10/ticket/12603).  
  
I generated these .inc files by running re2c with the `-b` option only, which appears to be how the original ones were created.

---

## Review 1 [Approved]

> Username: hkaiser  
> Created_at: 2017-10-25 00:42:24 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/20#pullrequestreview-71714814  

Marvelous, thanks so much!

---
