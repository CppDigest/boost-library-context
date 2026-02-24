# #500 - Documentation for process_stdio wrong with respect to file handles [Closed]

> Username: martin5233  
> Created at: 2025-06-26 08:22:14 UTC  
> Updated at: 2025-06-26 15:17:23 UTC  
> Closed at: 2025-06-26 15:17:23 UTC  
> Url: https://github.com/boostorg/process/issues/500  

The documentation in Boost 1.88 states, that the member notation from C++17 was stdin, stdout and stderr. However the implementation uses in, out and err.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2025-06-26 14:15:31 UTC  
> Url: https://github.com/boostorg/process/issues/500#issuecomment-3008656233  

Where?

---

## Comment 2

> Username: martin5233  
> Created at: 2025-06-26 14:48:15 UTC  
> Url: https://github.com/boostorg/process/issues/500#issuecomment-3008764089  

Search for C++17 in stdio.h. Right below it says:  
  
` * v2::process proc17(ctx, "/bin/bash", {}, v2::process_stdio{.stderr=nullptr});`  
  
which should be  
  
` * v2::process proc17(ctx, "/bin/bash", {}, v2::process_stdio{.err=nullptr});`

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2025-06-26 15:16:41 UTC  
> Url: https://github.com/boostorg/process/issues/500#issuecomment-3008856932  

Thanks, I searched for `.stdin` and didn't find it.
