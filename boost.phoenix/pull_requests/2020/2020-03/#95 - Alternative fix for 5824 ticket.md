# #95 Alternative fix for 5824 ticket [Open]

> Username: Kojoley  
> Created at: 2020-03-25 16:09:16 UTC  
> Updated at: 2020-03-27 18:04:25 UTC  
> Url: https://github.com/boostorg/phoenix/pull/95  

Just put `statement/sequence.hpp` into `operator.hpp`, because `operator,` is logically expected in operators include, while `scope/local_variable.hpp` does not make any sense (includes `statement/sequence.hpp` by the luck).

---

## Comment 1

> Username: Kojoley  
> Created_at: 2020-03-27 18:04:25 UTC  
> Url: https://github.com/boostorg/phoenix/pull/95#issuecomment-605173106  

CI fails are not related to the PR (some Appveyor-GitHub connection issues)

---
