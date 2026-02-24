# #299 - bug: Travis Build is Failing. [Closed]

> Username: snitin315  
> Created at: 2020-01-11 09:57:34 UTC  
> Updated at: 2020-01-11 13:58:15 UTC  
> Closed at: 2020-01-11 11:33:55 UTC  
> Url: https://github.com/boostorg/math/issues/299  

Travis Build is failing for the `development` branch.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-01-11 11:33:55 UTC  
> Url: https://github.com/boostorg/math/issues/299#issuecomment-573308739  

I restarted the failed job and it's fine now... looks like random bad mojo...

---

## Comment 2

> Username: NAThompson  
> Created at: 2020-01-11 13:58:15 UTC  
> Url: https://github.com/boostorg/math/issues/299#issuecomment-573319243  

Yeah, actually the most common cause of build failures is network timeouts on travis's side. So the failing build is just a minor nuisance; not an indication that the health of the library is somehow worse.
