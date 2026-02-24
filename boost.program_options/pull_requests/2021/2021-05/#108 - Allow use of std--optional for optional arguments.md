# #108 Allow use of std::optional for optional arguments [Closed]

> Username: jonesmz  
> Created at: 2021-05-31 21:39:58 UTC  
> Updated at: 2024-03-05 20:59:11 UTC  
> Closed at: 2024-03-05 20:59:11 UTC  
> Url: https://github.com/boostorg/program_options/pull/108  

This is in addition to supporting boost::optional.

---

## Comment 1

> Username: sjoubert  
> Created_at: 2024-03-05 10:40:15 UTC  
> Url: https://github.com/boostorg/program_options/pull/108#issuecomment-1978461256  

Any update on merging this? This would be really nice to avoid having to convert `boost::optional` to `std::optional` after parsing.

---

## Comment 2

> Username: vprus  
> Created_at: 2024-03-05 11:43:15 UTC  
> Url: https://github.com/boostorg/program_options/pull/108#issuecomment-1978570784  

Does this PR even work? It refers to `std::optional` without ever including `<optional>`.   
  
Ideally, both include of the header and this new definition should be conditional on the compiler supporting C++17. Are you willing to make such changes?

---

## Comment 3

> Username: sjoubert  
> Created_at: 2024-03-05 13:18:48 UTC  
> Url: https://github.com/boostorg/program_options/pull/108#issuecomment-1978761412  

I quickly tested this and it seems to work. But yes, I might have some time to put into this. What should I check?  
* Conditional include and use of `std::optional`  
* Adding some tests I assume?  
* I don't think I can replace the current branch/PR, should I create a new one?

---

## Comment 4

> Username: vprus  
> Created_at: 2024-03-05 13:23:50 UTC  
> Url: https://github.com/boostorg/program_options/pull/108#issuecomment-1978770178  

Yeah, I think you need a new branch/PR. For tests, they will be appreciated, but it's up to you.

---

## Comment 5

> Username: jonesmz  
> Created_at: 2024-03-05 20:33:40 UTC  
> Url: https://github.com/boostorg/program_options/pull/108#issuecomment-1979590519  

the PR was pulled straight out of my work codebase's copy of boost. Used in production for over 5 years.  
  
Feel free to adjust the PR directly, or to use an alternative PR written by someone else.

---

## Comment 6

> Username: vprus  
> Created_at: 2024-03-05 20:59:11 UTC  
> Url: https://github.com/boostorg/program_options/pull/108#issuecomment-1979625931  

Folks,   
thanks for your interest and contribution; this is now merged in https://github.com/boostorg/program_options/pull/132.

---
