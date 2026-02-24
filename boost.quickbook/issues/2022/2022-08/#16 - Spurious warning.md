# #16 - Spurious warning [Open]

> Username: vinniefalco  
> Created at: 2022-08-05 05:13:27 UTC  
> Updated at: 2022-08-05 05:13:27 UTC  
> Url: https://github.com/boostorg/quickbook/issues/16  

This produces a warning when you write `[br]` in a QBK file:  
  
https://github.com/boostorg/quickbook/blob/5f2e8c24cb813b4b0b94d3316e664b971c5b71dd/src/actions.cpp#L265  
  
Thing is... it works fine. There is no other "boostbook processor" on the horizon, so the warning is rather pointless. We want to make doc warnings into errors in our CI builds but this is in the way.
