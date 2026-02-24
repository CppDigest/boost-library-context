# #104 fix missing header [Merged]

> Username: davydden  
> Created at: 2018-03-31 08:39:25 UTC  
> Updated at: 2018-04-04 19:59:30 UTC  
> Merged at: 2018-04-03 23:21:44 UTC  
> Closed at: 2018-04-03 23:21:44 UTC  
> Url: https://github.com/boostorg/graph/pull/104  

following https://svn.boost.org/trac10/ticket/13505#comment:4 I create a PR here  
  
p.s. https://github.com/boostorg/graph/pull/101 is a better fix, but I would really like to have this fixed for 1.67.0 as this is a regression

---

## Comment 1

> Username: morinmorin  
> Created_at: 2018-04-04 12:13:15 UTC  
> Url: https://github.com/boostorg/graph/pull/104#issuecomment-378578619  

@danieljames  
Thanks for merging!  
  
@davydden  
Could you make a comment in PR #101 that asks for updating the PR to delete the newly added line `#include <boost/iterator.hpp>`? Thanks in advance.  
  
Regards,  
Michel Morin

---

## Comment 2

> Username: DanielaE  
> Created_at: 2018-04-04 14:49:20 UTC  
> Url: https://github.com/boostorg/graph/pull/104#issuecomment-378627435  

Michel, I've rebased PR #101 accordingly.  
  
Ciao  
  Dani

---

## Comment 3

> Username: morinmorin  
> Created_at: 2018-04-04 17:39:51 UTC  
> Url: https://github.com/boostorg/graph/pull/104#issuecomment-378684377  

Thanks for the rebase!  
Hopefully, Daniel maybe merge the PR after the release of Boost 1.67.

---

## Comment 4

> Username: danieljames  
> Created_at: 2018-04-04 18:03:20 UTC  
> Url: https://github.com/boostorg/graph/pull/104#issuecomment-378691694  

No promises, the most important thing to do is try to get existing changes on develop ready for release, and fix test failures where possible.

---

## Comment 5

> Username: morinmorin  
> Created_at: 2018-04-04 19:59:30 UTC  
> Url: https://github.com/boostorg/graph/pull/104#issuecomment-378726257  

Oh, I didn't notice that there are many commits on develop that are not merged to master yet. I understand that cleaning up these discrepancies and fixing test failures have first priority.

---
