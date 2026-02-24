# #83 - [docs] Index is missing [Closed]

> Username: Lastique  
> Created at: 2024-02-04 22:15:23 UTC  
> Updated at: 2024-03-17 22:55:53 UTC  
> Closed at: 2024-03-17 22:55:53 UTC  
> Url: https://github.com/boostorg/intrusive/issues/83  

At least in Boost 1.84.0, the Indexes section in the docs seem to be not compiled correctly:  
  
https://www.boost.org/doc/libs/1_84_0/doc/html/intrusive/index.html

---

## Comment 1

> Username: igaztanaga  
> Created at: 2024-03-17 22:48:50 UTC  
> Url: https://github.com/boostorg/intrusive/issues/83#issuecomment-2002644671  

It seems that the index was lost between between Boost 1.58 and Boost 1.59. After investigating the reason for longer than I'd expect, it seems that upgrading the quickbook languange version from [quickbook 1.5] to [quickbook 1.6] broke the index inclusion.  
  
I'll follow Boost.Math's index generation commands that are compatible with Quickbook 1.7. Many thanks for the issue.
