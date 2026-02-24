# #70 doc: add PROVIDES_EXECUTORS to the config table [Merged]

> Username: mauve  
> Created at: 2015-10-10 07:13:38 UTC  
> Updated at: 2015-10-10 11:43:05 UTC  
> Merged at: 2015-10-10 08:26:02 UTC  
> Closed at: 2015-10-10 08:26:02 UTC  
> Url: https://github.com/boostorg/thread/pull/70  



---

## Comment 1

> Username: mauve  
> Created_at: 2015-10-10 09:03:37 UTC  
> Url: https://github.com/boostorg/thread/pull/70#issuecomment-147067179  

Did you see my email where I was confused that could not find where _PROVIDES_EXECUTORS is not defined in config.hpp? This PR says (probably incorrectly ) that executors are provided when v4 is used. Do you want me to update the table or shall I fix config.hpp?

---

## Comment 2

> Username: viboes  
> Created_at: 2015-10-10 11:43:05 UTC  
> Url: https://github.com/boostorg/thread/pull/70#issuecomment-147078185  

Yes, I read it, but too late :(  
Don't worry I will fix the documentation, as I prefer to don't provide it in v4, because the `async()`/`then()` interface changes.  
  
I can include it in V5, but as the interface is not yet stable I prefer to wait.  
  
Other changes in V5 will be to move to non-blocking futures for `then()`and having light Executors (copyable executors).  
  
Vladimir requests associating an Executor to the continuation of a future will be also part of the new version.   
  
I'm not sure yet if I could have these branches ready for boost1.60.

---
