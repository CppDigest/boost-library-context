# #343 - Per-commit binary size measurements [Closed]

> Username: vinniefalco  
> Created at: 2020-09-15 15:39:18 UTC  
> Updated at: 2020-10-09 18:55:45 UTC  
> Closed at: 2020-10-09 18:55:45 UTC  
> Url: https://github.com/boostorg/json/issues/343  

We need metrics on the size of the resulting executable which performs serialization and parsing, per-commit, to measure the effects of changes and detect regressions.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-09-15 15:41:24 UTC  
> Url: https://github.com/boostorg/json/issues/343#issuecomment-692801056  

> pdimov: the more important thing is to set up metrics first, then do stuff  
  
Add another program in the bench/ directory called size which serializes and parses, and then add a Travis target that compiles it with the configurations that Peter gave you, and outputs the resulting size

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-10-09 18:55:45 UTC  
> Url: https://github.com/boostorg/json/issues/343#issuecomment-706349919  

we have the bloaty mcbloatface per-commit workflow now
