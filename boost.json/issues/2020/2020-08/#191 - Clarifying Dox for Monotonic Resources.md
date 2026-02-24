# #191 - Clarifying Dox for Monotonic Resources [Closed]

> Username: JonKalb  
> Created at: 2020-08-23 18:44:18 UTC  
> Updated at: 2020-08-24 22:02:46 UTC  
> Closed at: 2020-08-24 22:02:46 UTC  
> Url: https://github.com/boostorg/json/issues/191  

On this page: http://vinniefalco.github.io/doc/json/json/usage/allocators.html  
The first paragraph under Monotonic Resources contains a sentence that begins _This implementation allocates large blocks of memory and then allocates within these blocks to satisfy allocation requests…_. This sentence uses the word _allocates_ twice, but the two uses refer to different actions which could confuse some users.  
  
Suggesting: _This implementation **acquires** large blocks of memory and then allocates from **within** these blocks to satisfy allocation requests…_

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-08-23 19:31:22 UTC  
> Url: https://github.com/boostorg/json/issues/191#issuecomment-678814806  

ooh nice wording

---

## Comment 2

> Username: sdkrystian  
> Created at: 2020-08-24 22:02:46 UTC  
> Url: https://github.com/boostorg/json/issues/191#issuecomment-679389265  

Closed by 5dc6125352be1e03c157e868a2d5a7f3363dd6da
