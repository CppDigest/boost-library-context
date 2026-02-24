# #47 Fix node 20 issue on GHA CI [Open]

> Username: Flamefire  
> Created at: 2024-12-07 18:54:59 UTC  
> Updated at: 2025-02-02 11:01:45 UTC  
> Url: https://github.com/boostorg/bimap/pull/47  



---

## Comment 1

> Username: joaquintides  
> Created_at: 2024-12-08 11:20:57 UTC  
> Url: https://github.com/boostorg/bimap/pull/47#issuecomment-2525658538  

Hi @Flamefire, thanks for your contribution. Tests still fail for these jobs:  
  
* https://github.com/boostorg/bimap/actions/runs/12215326386/job/34089119859?pr=47  
* https://github.com/boostorg/bimap/actions/runs/12215326386/job/34089121257?pr=47  
* https://github.com/boostorg/bimap/actions/runs/12215326386/job/34089121354?pr=47  
* https://github.com/boostorg/bimap/actions/runs/12215326386/job/34089121505?pr=47  
  
Any idea what's going on?

---

## Comment 2

> Username: Flamefire  
> Created_at: 2024-12-08 12:57:38 UTC  
> Url: https://github.com/boostorg/bimap/pull/47#issuecomment-2525807622  

Some containers that don't need to be used. Reformatted to verify against the boost-ci template

---

## Comment 3

> Username: pdimov  
> Created_at: 2025-01-27 21:04:43 UTC  
> Url: https://github.com/boostorg/bimap/pull/47#issuecomment-2616890411  

Why are you setting `toolset` to the wrong value (`toolset: clang++-3.9` instead of `toolset: clang-3.9`), and then patching it back in the script?

---

## Comment 4

> Username: Flamefire  
> Created_at: 2025-02-02 10:59:27 UTC  
> Updated_at: 2025-02-02 11:01:45 UTC  
> Url: https://github.com/boostorg/bimap/pull/47#issuecomment-2629345250  

> Why are you setting `toolset` to the wrong value (`toolset: clang++-3.9` instead of `toolset: clang-3.9`), and then patching it back in the script?  
  
Not really required in this repo indeed. That was from a RegEx replacement to ensure the correct compiler is used. IIRC I temporarily used `matrix.toolset` as the compiler in the user config but then decided against it.     
Changed

---
