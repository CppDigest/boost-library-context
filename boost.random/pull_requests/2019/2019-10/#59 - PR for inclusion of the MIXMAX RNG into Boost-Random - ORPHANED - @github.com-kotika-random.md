# #59 PR for inclusion of the MIXMAX RNG into Boost/Random - ORPHANED - @github.com/kotika/random  [Closed]

> Username: kotika  
> Created at: 2019-10-18 12:34:25 UTC  
> Updated at: 2020-07-07 23:32:28 UTC  
> Closed at: 2020-07-07 23:32:28 UTC  
> Url: https://github.com/boostorg/random/pull/59  



---

## Comment 1

> Username: swatanabe  
> Created_at: 2019-10-24 00:17:11 UTC  
> Url: https://github.com/boostorg/random/pull/59#issuecomment-545688354  

AMDG  
  
- I don't see anything particularly useful about mixmax_demo  
- Don't remove unrelated code  
- The README.md is not appropriate for inclusion in Boost.  
- indentation is inconsistent  
- In for loops, you should declare the loop counter in the  
  loop initialization, rather than at the top of the  
  function.  In general declare variables as locally as  
  possible and initialize them at the point of definition.  
- Please remove the debug prints  
- Is there a reason for using getline in operator>> instead  
  of just is >> std::ws >> counter, for example?  
- Your doc paragraph is duplicated.  Also, I'd prefer to  
  keep only the first half of this, and leave out the  
  discussion of specific engines.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: kotika  
> Created_at: 2019-10-29 12:13:33 UTC  
> Url: https://github.com/boostorg/random/pull/59#issuecomment-547390934  

Link to original repo orphaned, go to https://github.com/kotika/random  
for the new repo.

---
