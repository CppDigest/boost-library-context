# #12 Removed disjoint_sets exclusion [Closed]

> Username: jeking3  
> Created at: 2019-04-26 19:47:51 UTC  
> Updated at: 2019-05-02 18:07:01 UTC  
> Closed at: 2019-05-02 18:07:01 UTC  
> Url: https://github.com/boostorg/boostdep/pull/12  

This should happen soon after commit of https://github.com/boostorg/graph/pull/169

---

## Comment 1

> Username: jeking3  
> Created_at: 2019-05-02 17:40:20 UTC  
> Url: https://github.com/boostorg/boostdep/pull/12#issuecomment-488764575  

disjoint_sets was merged into boostorg/graph.  This resolved a cyclic dependency and disjoint_sets will no longet be a standalone project.  This can be committed to develop once the commit in the superproject is committed.  See https://github.com/boostorg/boost/pull/283

---

## Comment 2

> Username: pdimov  
> Created_at: 2019-05-02 18:07:01 UTC  
> Url: https://github.com/boostorg/boostdep/pull/12#issuecomment-488773701  

Exceptions updated in https://github.com/boostorg/boostdep/commit/b87f4a3bdc316c354121057508d45c9c161ca28d.

---
