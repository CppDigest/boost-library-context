# #59 Inheriting std::iterator is deprecated in c++17. [Merged]

> Username: DanielaE  
> Created at: 2017-12-28 12:08:03 UTC  
> Updated at: 2018-01-30 18:47:28 UTC  
> Merged at: 2018-01-28 12:41:19 UTC  
> Closed at: 2018-01-28 12:41:19 UTC  
> Url: https://github.com/boostorg/phoenix/pull/59  

Therefore get rid of all of that and replace inheritance by lifting std::iterator's members into the derived class.  
  
Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Comment 1

> Username: Flast  
> Created_at: 2018-01-15 14:56:22 UTC  
> Url: https://github.com/boostorg/phoenix/pull/59#issuecomment-357705292  

LGTM and thanks Daniela!  
  
But we are now enabling CIs (#60). Please wait until that work has been completed.

---

## Comment 2

> Username: Flast  
> Created_at: 2018-01-24 04:48:47 UTC  
> Url: https://github.com/boostorg/phoenix/pull/59#issuecomment-360019899  

I try closing and reopening to trigger brand new build on Travis.

---

## Comment 3

> Username: Flast  
> Created_at: 2018-01-24 04:49:37 UTC  
> Url: https://github.com/boostorg/phoenix/pull/59#issuecomment-360020011  

Yeah! New build has been queued!

---

## Comment 4

> Username: Flast  
> Created_at: 2018-01-28 12:41:14 UTC  
> Url: https://github.com/boostorg/phoenix/pull/59#issuecomment-361059968  

(Again) LGTM, thanks!

---
