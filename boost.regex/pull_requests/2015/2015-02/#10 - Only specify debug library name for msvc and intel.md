# #10 Only specify debug library name for msvc and intel [Merged]

> Username: jhunold  
> Created at: 2015-02-09 11:10:45 UTC  
> Updated at: 2015-02-13 19:22:04 UTC  
> Merged at: 2015-02-13 19:22:03 UTC  
> Closed at: 2015-02-13 19:22:04 UTC  
> Url: https://github.com/boostorg/regex/pull/10  

Boost.Build can then fallback to the release libraries for these  
compilers for user-defined variants.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2015-02-09 17:16:54 UTC  
> Url: https://github.com/boostorg/regex/pull/10#issuecomment-73548767  

Can you elaborate a little on this?  As far as I know, you must link to ICU binaries built using the same options as your program (ie runtime lib variant) otherwise really bad thing happen.

---

## Comment 2

> Username: jhunold  
> Created_at: 2015-02-09 18:09:07 UTC  
> Url: https://github.com/boostorg/regex/pull/10#issuecomment-73558360  

Basically I am using my "<variant>develop" for years. This is just "release" with debug symbols enabled so I get a minimal backtrace while having fast computation and quite noisy logfiles. This setup is link-compatible with the (Boost-)libraries produces by <variant>release. Hardcoding the library names for both <debug> and <release> prevents Boost.Build from chosing a valid alternative when encountering <variant>develop. So this patch fixes my case while not breaking the other usages. As soon as you need advanced settings (iterator_debugging etc.) this selection magic will break into much more pieces.

---

## Comment 3

> Username: jhunold  
> Created_at: 2015-02-11 16:19:22 UTC  
> Url: https://github.com/boostorg/regex/pull/10#issuecomment-73909997  

John, do you need more information?

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2015-02-11 17:41:04 UTC  
> Url: https://github.com/boostorg/regex/pull/10#issuecomment-73926438  

> John, do you need more information?  
  
No, I just want to test it locally - and as I'm recovering from laptop   
failure and don't have have the things I need installed yet it may be a   
couple of days :(  
  
Will get back to you shortly, John.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2015-02-13 19:21:59 UTC  
> Url: https://github.com/boostorg/regex/pull/10#issuecomment-74310584  

Tested OK.

---
