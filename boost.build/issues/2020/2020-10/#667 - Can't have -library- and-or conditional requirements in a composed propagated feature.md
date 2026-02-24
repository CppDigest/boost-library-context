# #667 - Can't have <library> and/or conditional requirements in a composed propagated feature. [Open]

> Username: atlantisjk  
> Created at: 2020-10-30 17:52:47 UTC  
> Updated at: 2021-05-29 16:23:12 UTC  
> Url: https://github.com/boostorg/build/issues/667  

A possible [solution](https://stackoverflow.com/a/45428334/14396720) does not work. I got `error: expansions of composite features result in conflicting values`.  
  
I want to conditionally use a library (with alternatives) for targets. I don't want to add conditions everywhere but prefer to create a propagated feature. However, I got `error: Recursion in main target references`. The question is also mentioned [here](https://stackoverflow.com/q/64613164/14396720).

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-05-29 16:22:42 UTC  
> Url: https://github.com/boostorg/build/issues/667#issuecomment-850859646  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
