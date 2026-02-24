# #225 - Use concepts for detecting method presence [Open]

> Username: psiha  
> Created at: 2025-04-22 09:28:26 UTC  
> Updated at: 2025-04-26 22:44:52 UTC  
> Url: https://github.com/boostorg/parser/issues/225  

Ideally I'd ask if you could 'simply' drop precpp20 (or even 23) support 😊 but if that is not feasible can you please use concepts (instead of SFINAE and old meta tricks) wherever possible or at least for detecting the presence of certain methods (like push_back) - with your current approach I got [compilation errors](https://github.com/psiha/vm/pull/53/commits/ad8ab4ee5960fa446d8238d14ab52016b74782ac) w/ _explicit object parameter_ and constructs like `T{}.push_back()` (as the T{} rvalue cannot bind to an lvalue reference like `this T &`).

---

## Comment 1

> Username: tzlaine  
> Created at: 2025-04-26 22:44:51 UTC  
> Url: https://github.com/boostorg/parser/issues/225#issuecomment-2832682130  

It would probably be better to use concepts in C++20 and later for this.  This is a pretty low priority for me though.  I'll probably get to it eventually, but if you want it fast, a PR would be welcome.
