# #521 - [windows] missing return statements [Closed]

> Username: Eddie-cz  
> Created at: 2025-10-06 12:26:36 UTC  
> Updated at: 2025-10-07 15:55:47 UTC  
> Closed at: 2025-10-07 15:55:47 UTC  
> Url: https://github.com/boostorg/process/issues/521  

In file: process/v2/windows/default_launcher.hpp  
Functions auto invoke_on_error(...) and auto invoke_on_success(...) are missing return statements, compiler warns about it.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2025-10-06 13:25:29 UTC  
> Url: https://github.com/boostorg/process/issues/521#issuecomment-3371639856  

They return void.

---

## Comment 2

> Username: Eddie-cz  
> Created at: 2025-10-06 14:25:33 UTC  
> Url: https://github.com/boostorg/process/issues/521#issuecomment-3371964670  

Ok, but imho it's unneccessary to let compiler warn about it when all is needed are two returns.

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2025-10-07 12:26:38 UTC  
> Url: https://github.com/boostorg/process/issues/521#issuecomment-3376662938  

What warning are you getting?

---

## Comment 4

> Username: Eddie-cz  
> Created at: 2025-10-07 12:51:09 UTC  
> Url: https://github.com/boostorg/process/issues/521#issuecomment-3376754960  

Your comment thay they return void helped to understand it.  
We were confused with auto invoke_on_error(...)->decltype(init.on_error(launcher, executable, cmd_line, ec)) function declaration  
and passed bp2::error_code on_success(bp2::default_process_launcher&, ....) in Init structure.  
Thanks for clarification

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2025-10-07 14:58:12 UTC  
> Url: https://github.com/boostorg/process/issues/521#issuecomment-3377308949  

Ok great, it's working as intended :)
