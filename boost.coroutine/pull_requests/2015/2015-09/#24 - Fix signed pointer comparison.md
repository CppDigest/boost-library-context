# #24 Fix signed pointer comparison. [Merged]

> Username: BenWiederhake  
> Created at: 2015-09-17 17:39:10 UTC  
> Updated at: 2015-09-17 17:56:07 UTC  
> Merged at: 2015-09-17 17:50:02 UTC  
> Closed at: 2015-09-17 17:50:02 UTC  
> Url: https://github.com/boostorg/coroutine/pull/24  

I created this using find, grep, and sed. No manual editing, but lots of manual inspection. I'm pretty sure that this is correct, and I'm currently running all regression tests. I'll follow up on whether they are successful or not, but just wanted to provide grounds for discussion for #23 .

---

## Comment 1

> Username: olk  
> Created_at: 2015-09-17 17:50:12 UTC  
> Url: https://github.com/boostorg/coroutine/pull/24#issuecomment-141164860  

thx

---

## Comment 2

> Username: BenWiederhake  
> Created_at: 2015-09-17 17:56:07 UTC  
> Url: https://github.com/boostorg/coroutine/pull/24#issuecomment-141166626  

After some initial problems with generally building it at all (libbz-dev was missing), all regression tests pass on my machine.

---
