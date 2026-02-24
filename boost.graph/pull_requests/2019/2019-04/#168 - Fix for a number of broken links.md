# #168 Fix for a number of broken links. [Merged]

> Username: tinko92  
> Created at: 2019-04-24 07:08:35 UTC  
> Updated at: 2019-04-24 17:48:40 UTC  
> Merged at: 2019-04-24 17:48:40 UTC  
> Closed at: 2019-04-24 17:48:40 UTC  
> Url: https://github.com/boostorg/graph/pull/168  

Hi,  
  
sorry for this pretty large commit. It is intended to fix a large number of broken external links in the Boost.Graph library. Here is what it does:  
  
1. Replaces all instances of http://www.sgi.com/tech with https://boost.org/sgi .  
2. Replaces all instances of http://www.osl.iu.edu/~lums with https://homes.cs.washington.edu/~al75 . I hope, this is appropriate, it is his most current homepage I could find.  
3. Replaces all instances of http://www.cs.rpi.edu/~beevek with http://cs.krisbeevers.com (See 2) )  
4. Replaces links to the old qhull websites with links to qhull.org  
5. Replace two broken deep links to pdfs of papers with links to the ResearchGate page holding the full-text pdfs.  
6. Numerous links that are dead were prefixed with webarchive-links, so the old content is shown. I hope the chosen dates are appropriate but ultimately I was not able to confirm whether I've linked the correct versions so I used my best judgment.  
7. Replaced a dead link to an e-mail in the boost mailing list archive with a link to (almost surely) the same e-mail in the current archive. The old e-mail is not in the web archive, but if the list of alternative urls in this https://www.mail-archive.com/boost@lists.boost.org/msg09308.html is accurate, then it should be the same. It makes sense in the context.  
8. Some more link updates.   
  
If you want me break this up into smaller commits or revert some of the changes, please let me know.  
  
Kind regards,  
Tinko Bartels  
  
(Edit: I did not build the tests because I was not changing any actual code and because, in case I messed up, I am still trusting the CI to do that.)

---

## Comment 1

> Username: anadon  
> Created_at: 2019-04-24 12:19:33 UTC  
> Url: https://github.com/boostorg/graph/pull/168#issuecomment-486204296  

@jzmaddock Can we get this merged in quickly?  It looks good to me.

---
