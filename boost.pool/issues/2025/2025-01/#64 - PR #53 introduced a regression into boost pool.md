# #64 - PR #53 introduced a regression into boost pool [Open]

> Username: ni-vzavalishin  
> Created at: 2025-01-15 14:11:33 UTC  
> Updated at: 2025-01-15 14:11:33 UTC  
> Url: https://github.com/boostorg/pool/issues/64  

**TL;DR**: PR #53 seems to be based on a wrong assumption. In fact,`simple_segregated_storage` in its present form is fundamentally uncapable of handling mixed partition sizes, and cannot be "fixed" to allow that handling without a major change of the implementation. The linked-to [Example 4.1](https://theboostcpplibraries.com/boost.pool#ex.pool_01), which was a starting point for the "fix", also looks simply wrong and so does the fix itself.  
  
There is a [new PR here](https://github.com/boostorg/pool/pull/63), which contains further details, including  
- the demonstration of the regression (the 1st commit of the new PR),  
- the reverting of the code change (2nd commit of the new PR) and  
-  dropping of the two failing uint tests introduced by PR #53, which are believed to be incorrect.
