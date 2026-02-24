# #668 Type cast (to suppress warnings) [Closed]

> Username: kojiynet  
> Created at: 2021-07-25 08:03:00 UTC  
> Updated at: 2021-07-25 08:16:38 UTC  
> Closed at: 2021-07-25 08:16:38 UTC  
> Url: https://github.com/boostorg/math/pull/668  



---

## Comment 1

> Username: kojiynet  
> Created_at: 2021-07-25 08:13:13 UTC  
> Url: https://github.com/boostorg/math/pull/668#issuecomment-886164678  

When correlation_coefficient_seq_impl() tries to return a tuple, the type of "i" is assume to be Real, while actually its type is size_t. This makes VC issue warnings. To suppress this we might want to write "Real(i)" instead of "i". This is also consistent with "Real(0)" and "Real(1)" in the same return statements.

---

## Comment 2

> Username: kojiynet  
> Created_at: 2021-07-25 08:16:31 UTC  
> Url: https://github.com/boostorg/math/pull/668#issuecomment-886165064  

Sorry, I made mistakes in modifying the file..

---
