# #108 Change capital variable names to lowercase [Closed]

> Username: AdamMajer  
> Created at: 2017-01-18 13:31:01 UTC  
> Updated at: 2017-02-19 09:19:32 UTC  
> Closed at: 2017-02-19 09:19:32 UTC  
> Url: https://github.com/boostorg/test/pull/108  

Capitals clash by convention with #define, and in this case  
VERSION clashes with GNU Autotools. This means projecting using  
GNU Autotools and Boost::Test library no longer compile.  
  
To fix this problem and possibly others down the line,  
downcase all the string constants in the rt::runtime_config  
namespace. (trac 12748)

---

## Comment 1

> Username: AdamMajer  
> Created_at: 2017-01-18 13:31:57 UTC  
> Url: https://github.com/boostorg/test/pull/108#issuecomment-273475673  

As to obvious "why not just change VERSION to something else?" - so it's consistent.

---

## Comment 2

> Username: raffienficiaud  
> Created_at: 2017-01-30 07:45:07 UTC  
> Url: https://github.com/boostorg/test/pull/108#issuecomment-275997517  

Thanks for the PR. While I understand for the `VERSION` macro clash, I would like to keep this minimal. So I would rather rework your PR for fixing this issue and making those variables/macros more boost.test specific.

---

## Comment 3

> Username: raffienficiaud  
> Created_at: 2017-01-30 08:15:32 UTC  
> Url: https://github.com/boostorg/test/pull/108#issuecomment-276001758  

Merged to branch `origin/topic/12748-VERSION-macro-clashes`, thanks

---

## Comment 4

> Username: AdamMajer  
> Created_at: 2017-01-30 09:46:33 UTC  
> Url: https://github.com/boostorg/test/pull/108#issuecomment-276018633  

Thanks fine too. Thank you.

---

## Comment 5

> Username: raffienficiaud  
> Created_at: 2017-02-19 09:19:32 UTC  
> Url: https://github.com/boostorg/test/pull/108#issuecomment-280906451  

Merged to develop, closing.

---
