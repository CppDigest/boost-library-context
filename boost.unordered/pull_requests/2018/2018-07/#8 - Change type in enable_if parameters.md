# #8 Change type in enable_if parameters [Closed]

> Username: sam-lunt  
> Created at: 2018-07-09 16:24:32 UTC  
> Updated at: 2023-03-07 18:42:21 UTC  
> Closed at: 2023-03-07 18:42:20 UTC  
> Url: https://github.com/boostorg/unordered/pull/8  

When compiling with `-Wzero-as-null-pointer-constant`, parameters such as `typename boost::enable_if_c<true, void*>::type = 0` generate a warning. By changing `void*` to `int`, no warning should be generated.

---

## Comment 1

> Username: cmazakas  
> Created_at: 2021-11-15 17:24:10 UTC  
> Url: https://github.com/boostorg/unordered/pull/8#issuecomment-969137560  

@sam-lunt Are you still interested in getting this PR merged? I've noted that it's quite old and has fallen behind develop quite a bit.  
  
If you can, please rebase and we'll get the updated CI to run.

---

## Comment 2

> Username: sam-lunt  
> Created_at: 2021-11-15 17:51:56 UTC  
> Url: https://github.com/boostorg/unordered/pull/8#issuecomment-969164710  

@LeonineKing1199 I rebased onto the newest version of develop. I think the changes are still valid as-is.

---

## Comment 3

> Username: cmazakas  
> Created_at: 2021-11-15 18:06:27 UTC  
> Url: https://github.com/boostorg/unordered/pull/8#issuecomment-969177795  

Hmm, seems like the GitHub Actions weren't triggered.  
  
I think I've run into this issue in the past, actually. If you close and re-open the PR, the GHA tests should start running.

---

## Comment 4

> Username: sam-lunt  
> Created_at: 2021-11-16 15:06:53 UTC  
> Url: https://github.com/boostorg/unordered/pull/8#issuecomment-970363905  

@LeonineKing1199 looks like the checks ran (and passed). I think it was waiting for approval from a project maintainer, so assuming that was you, thanks!

---

## Comment 5

> Username: cmazakas  
> Created_at: 2021-11-17 20:38:01 UTC  
> Url: https://github.com/boostorg/unordered/pull/8#issuecomment-971980015  

Alright, so I kicked this PR around to a few of the Boost authors and the direction we'd rather go is instead of changing the type in the `enable_if`s as they are now to silence the warning, we should be putting this `enable_if` in the return type.  
  
If you're up for it, feel free to make the refactors. Otherwise, I'll just go ahead and update the code myself. Not a big deal either way.

---

## Comment 6

> Username: sam-lunt  
> Created_at: 2021-11-18 02:25:42 UTC  
> Url: https://github.com/boostorg/unordered/pull/8#issuecomment-972454700  

My personal preference is actually to use a dummy template parameter. I think it's the most readable, since it doesn't obscure the function signature, it keeps the template parameter closer to the template constraints, and it can work with the default value for the second arg to `enable_if`:  
``` cpp  
template <class I, class = typename boost::unordered::detail::enable_if_forward<I>::type>  
inline std::size_t insert_size(I i, I j)  
```  
  
That said, it's of course your prerogative to use whichever option you prefer. They'll be no hard feelings on my end : ) but I think it'll probably be faster if you make the update yourself, I don't think I'll have the time for a bit.

---

## Comment 7

> Username: cmazakas  
> Created_at: 2021-11-18 17:49:44 UTC  
> Url: https://github.com/boostorg/unordered/pull/8#issuecomment-973112399  

> That said, it's of course your prerogative to use whichever option you prefer. They'll be no hard feelings on my end : ) but I think it'll probably be faster if you make the update yourself, I don't think I'll have the time for a bit.  
  
Heh, I feel that. I have a 9 month old at home and yeah, it's pretty much impossible to find free time (and more importantly energy) for anything :P

---

## Comment 8

> Username: sam-lunt  
> Created_at: 2021-11-18 18:46:03 UTC  
> Url: https://github.com/boostorg/unordered/pull/8#issuecomment-973155619  

Ha, in that case, I know _exactly_ how you feel: just replace "9 month" with "5 month" : )

---

## Comment 9

> Username: cmazakas  
> Created_at: 2023-03-07 18:42:20 UTC  
> Url: https://github.com/boostorg/unordered/pull/8#issuecomment-1458653201  

Seems like it's been long enough. I'm going to be closing this.

---
