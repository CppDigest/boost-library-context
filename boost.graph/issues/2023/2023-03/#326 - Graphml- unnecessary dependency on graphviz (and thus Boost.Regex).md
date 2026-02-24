# #326 - Graphml: unnecessary dependency on graphviz (and thus Boost.Regex) [Closed]

> Username: francoisk  
> Created at: 2023-03-07 14:29:04 UTC  
> Updated at: 2023-12-04 07:11:57 UTC  
> Closed at: 2023-12-04 07:11:57 UTC  
> Url: https://github.com/boostorg/graph/issues/326  

## Context/background  
We have [packaged](https://github.com/build2-packaging/boost) Boost for [build2](https://build2.org/) and are about to release changes that reduce the number of dependencies on Boost.Regex.  
  
The way we have done this in our Boost.Graph package is to make the graphviz "submodule"---which is the only part that logically depends on Boost.Regex---optional and disabled by default. When it is disabled none of the graphviz-related headers are installed.  
  
## The problem  
However, `graphml.hpp` includes `graphviz.hpp`, making it impossible to disable graphviz without also disabling graphml, and this for nothing but a few exception structs.  
  
## Suggested solution  
Our package's [patch](https://github.com/build2-packaging/boost/commit/e6ff2e3acf123509827dcd0c23e934f893cfb765) breaks this dependency by moving these exception structs to `exception.hpp` (which already contains exceptions that look similar/related to the ones in question) and including `exception.hpp` from `graphml.hpp` and `graphviz.hpp`.

---

## Comment 1

> Username: jeremy-murphy  
> Created at: 2023-03-09 23:52:46 UTC  
> Url: https://github.com/boostorg/graph/issues/326#issuecomment-1463006076  

Sounds great! Can you make a pull request with your patch here?

---

## Comment 2

> Username: jeremy-murphy  
> Created at: 2023-03-09 23:53:19 UTC  
> Url: https://github.com/boostorg/graph/issues/326#issuecomment-1463006376  

That Boost.Regex dependency has been a pain for a while, but I haven't had time to look into it.

---

## Comment 3

> Username: boris-kolpackov  
> Created at: 2023-03-10 06:07:24 UTC  
> Url: https://github.com/boostorg/graph/issues/326#issuecomment-1463322353  

> That Boost.Regex dependency has been a pain for a while, but I haven't had time to look into it.  
  
FYI, the direct dependency on `Boost.Regex` is only half of the problem: a number of other dependencies (I see `Boost.Algorithm`, `Boost.Range`, and `Boost.Serialization` right away) will pull `Boost.Regex` indirectly. But it's a step in the right direction.

---

## Comment 4

> Username: jeremy-murphy  
> Created at: 2023-12-03 22:30:49 UTC  
> Url: https://github.com/boostorg/graph/issues/326#issuecomment-1837620661  

That PR was merged, so does that resolve this particular issue?

---

## Comment 5

> Username: francoisk  
> Created at: 2023-12-04 07:11:57 UTC  
> Url: https://github.com/boostorg/graph/issues/326#issuecomment-1837961035  

Yes it does, thanks!
