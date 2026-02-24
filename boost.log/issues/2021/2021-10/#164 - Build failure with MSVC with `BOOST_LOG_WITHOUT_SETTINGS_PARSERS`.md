# #164 - Build failure with MSVC with `BOOST_LOG_WITHOUT_SETTINGS_PARSERS` [Closed]

> Username: Burgch  
> Created at: 2021-10-11 12:56:51 UTC  
> Updated at: 2021-10-24 13:16:49 UTC  
> Closed at: 2021-10-24 13:12:55 UTC  
> Url: https://github.com/boostorg/log/issues/164  

When `BOOST_LOG_WITHOUT_SETTINGS_PARSERS` is defined, the `boost_log_setup` library doesn't export any symbols (because all of the C++ sources are basically empty after pre-processing). This results in MSVC (2019 in this case, using Boost 1.75) skipping the `.lib` generation entirely, which then results in:  
  
```  
The system cannot find the file specified.  
...failed common.copy <path>\boost\lib\boost_log_setup-vc142-mt-x64-1_75.lib...  
```  
  
This led me to wonder whether there's even a need to build `boost_log_setup` at all when `BOOST_LOG_WITHOUT_SETTINGS_PARSERS` is defined? I have a proposed change which switches it between a library or an alias, depending on whether `BOOST_LOG_WITHOUT_SETTINGS_PARSERS` is defined or not - I'll create a pull request.

---

## Comment 1

> Username: Lastique  
> Created at: 2021-10-18 21:19:24 UTC  
> Url: https://github.com/boostorg/log/issues/164#issuecomment-946174999  

This is currently blocked by https://github.com/bfgroup/b2/issues/104.

---

## Comment 2

> Username: Lastique  
> Created at: 2021-10-24 13:13:55 UTC  
> Updated at: 2021-10-24 13:14:07 UTC  
> Url: https://github.com/boostorg/log/issues/164#issuecomment-950323470  

I've added a workaround for now. I will reevaluate disabling `boost_log_setup` proper once Boost.Build bug is fixed.  
  
Thanks for the report.

---

## Comment 3

> Username: Burgch  
> Created at: 2021-10-24 13:16:49 UTC  
> Url: https://github.com/boostorg/log/issues/164#issuecomment-950323892  

Thanks Andrey! If there’s no progress on the Boost.Build issue, I’d be happy to explain in more detail how the fix I proposed works if you’d rather use that long term.  
  
Best wishes,  
Conor  
  
> On 24 Oct 2021, at 14:14, Andrey Semashev ***@***.***> wrote:  
>   
> ﻿  
> I've added a workaround for now. I will reevaluate disabling boost_log_setup proper one Boost.Build bug is fixed.  
>   
> Thanks for the report.  
>   
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub, or unsubscribe.  
> Triage notifications on the go with GitHub Mobile for iOS or Android.
