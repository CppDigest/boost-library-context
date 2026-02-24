# #96 Moved graph_test.hpp from include directory to test directory. [Closed]

> Username: eldiener  
> Created at: 2017-07-31 03:14:14 UTC  
> Updated at: 2018-10-14 05:05:00 UTC  
> Closed at: 2018-10-14 05:03:16 UTC  
> Url: https://github.com/boostorg/graph/pull/96  



---

## Comment 1

> Username: anadon  
> Created_at: 2018-08-31 17:47:28 UTC  
> Url: https://github.com/boostorg/graph/pull/96#issuecomment-417740963  

This change looks like it could break things.  Could you please provide a more complete description of the change and tests to verify it does not break anything?

---

## Comment 2

> Username: eldiener  
> Created_at: 2018-08-31 18:27:18 UTC  
> Url: https://github.com/boostorg/graph/pull/96#issuecomment-417751945  

The graph_test.hpp is not used anywhere except in tests and is not documented as part of the graph interface.  There is no reason it should be part of the include directory, The boostdep tool to find dependencies normally searches the include directory of a library but not the test directory.

---

## Comment 3

> Username: anadon  
> Created_at: 2018-08-31 18:59:03 UTC  
> Url: https://github.com/boostorg/graph/pull/96#issuecomment-417760315  

Let me read into this to make sure everything is safe.  If so, I'll include this in the second batch of changes.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2018-10-12 18:16:45 UTC  
> Url: https://github.com/boostorg/graph/pull/96#issuecomment-429414339  

Looks sensible to me - closing and re-opening to trigger CI.

---

## Comment 5

> Username: pdimov  
> Created_at: 2018-10-14 05:03:16 UTC  
> Url: https://github.com/boostorg/graph/pull/96#issuecomment-429596965  

Did (and just merged) the same PR in #120 without seeing this one, sorry. :-)

---

## Comment 6

> Username: pdimov  
> Created_at: 2018-10-14 05:05:00 UTC  
> Url: https://github.com/boostorg/graph/pull/96#issuecomment-429597019  

(I needed to cut the dependency on Test because it interfered with the CMake config work.)

---
