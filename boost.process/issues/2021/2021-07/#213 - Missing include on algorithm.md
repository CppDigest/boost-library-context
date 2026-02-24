# #213 - Missing include on algorithm [Open]

> Username: Eddie-cz  
> Created at: 2021-07-19 10:18:13 UTC  
> Updated at: 2021-08-02 08:56:54 UTC  
> Url: https://github.com/boostorg/process/issues/213  

Error: boost/process/detail/traits/wchar_t.hpp(150,14): error : no member named 'transform' in namespace 'std'  
Fix: include \<algorithm\> in wchar_t.hpp  
Version: Boost 1.77 beta 1

---

## Comment 1

> Username: Eddie-cz  
> Created at: 2021-08-01 13:50:08 UTC  
> Url: https://github.com/boostorg/process/issues/213#issuecomment-890524243  

Is there anybody who is dealing with issues like this? For an year, I manually patch 036c9fc4d18719c5f6ddeb87419691c9f4ea4dc2, because it's still not in boost release. This lib is starting to be kinda annoying.

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2021-08-01 14:01:52 UTC  
> Url: https://github.com/boostorg/process/issues/213#issuecomment-890526129  

Sorry this fell of my radar. If you create a PR, I'll just merge it.

---

## Comment 3

> Username: Eddie-cz  
> Created at: 2021-08-02 08:56:54 UTC  
> Url: https://github.com/boostorg/process/issues/213#issuecomment-890852219  

PR #215
