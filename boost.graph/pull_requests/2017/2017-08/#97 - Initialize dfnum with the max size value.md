# #97 Initialize dfnum with the max size value [Closed]

> Username: scottgw  
> Created at: 2017-08-09 10:23:27 UTC  
> Updated at: 2019-01-02 07:07:57 UTC  
> Closed at: 2018-09-02 05:37:08 UTC  
> Url: https://github.com/boostorg/graph/pull/97  

The previous default value of 0 will, if not updated, pass the "skip unreachable nodes" in the dominator visitor. This leads to the pred_map containing a forest instead of a tree, and the forest won't contain a correct tree either (we can't filter the output to get what we want).  
  
This change attempts to solve this by making the second half of the check succeed "n > numVertices".

---

## Comment 1

> Username: anadon  
> Created_at: 2018-08-31 17:45:40 UTC  
> Url: https://github.com/boostorg/graph/pull/97#issuecomment-417740473  

I'm helping out with the PR backlog.  I don't immediately see the imperative of this change.  I also don't see a new test and it looks like you are thinking the change is a bugfix.  Could you please explain a bit more the need for this change and add a corresponding test?  This is to let you know and help me prioritize PR's.

---

## Comment 2

> Username: scottgw  
> Created_at: 2018-09-02 05:37:08 UTC  
> Url: https://github.com/boostorg/graph/pull/97#issuecomment-417906427  

Yeah, good point I can see how the change (after a year for myself) is hard to reconstitute the bug/fix without first re-familiarizing myself with the dominator calculation. Unfortunately at this point I moved off of this implementation and BGL entirely actually, so it's hard to justify actually doing that work.  
  
However, I can say that the implementation is most certainly broken, if you care in general about the health of the dominator library or know someone who is perhaps they can write an appropriate bug report.

---

## Comment 3

> Username: anadon  
> Created_at: 2018-09-02 14:09:47 UTC  
> Url: https://github.com/boostorg/graph/pull/97#issuecomment-417933514  

@scottgw If I can, I actually want to replace BGL entirely.  I've been fixing code this weekend and the internals are not stable in the long term.  But first, I need to get a somewhat fixed legacy version.

---

## Comment 4

> Username: scottgw  
> Created_at: 2019-01-02 07:07:57 UTC  
> Url: https://github.com/boostorg/graph/pull/97#issuecomment-450797692  

Sorry about the long delay; I've taken a second look at this and I *think* this was because of the check for "< 0" on unsigned types, which AFAIK the vertextype can be:  
https://github.com/scottgw/graph/blob/eef620ebb0e534d3c114d23a60ac59883beaa6da/include/boost/graph/dominator_tree.hpp#L128  
  
I think my intention was to make the second half of the check functional instead, skipping uninitialized nodes by having them be greater than (almost) anything.

---
