# #19 Fixing problem preventing building on VS2010 due to locate-default-setup rule being passed an undefined value. [Merged]

> Username: stgates  
> Created at: 2014-07-11 19:56:17 UTC  
> Updated at: 2021-10-02 22:35:53 UTC  
> Merged at: 2014-07-12 06:26:22 UTC  
> Closed at: 2014-07-12 06:26:22 UTC  
> Url: https://github.com/boostorg/build/pull/19  

I tested with 10.0, 11.0, 12.0, and 14.0 toolsets. The problem basically was phone-directory didn't have a value and then failure would occur calling the locate-default-setup rule. I changed to always initialize the phone-directory variable so this can't happen.  
  
I also improved the check for below-11.0 to better match what is done for below-8.0.   
  
Fixes issue mentioned in the following discussion:  
  
http://boost.2283326.n4.nabble.com/Can-t-run-tests-on-develop-tt4665008.html

---

## Comment 1

> Username: vprus  
> Created_at: 2014-07-12 06:27:35 UTC  
> Url: https://github.com/boostorg/build/pull/19#issuecomment-48803871  

I've merged this; thanks for speedy fix.  
  
In future, please use Linux convention for commit messages - there should be a single  
line, under 72 characters, describing the gist of the change, optionally followed by empty  
line and then further explanation.  
  
Thanks again!

---
