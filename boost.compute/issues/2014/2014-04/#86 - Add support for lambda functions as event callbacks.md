# #86 - Add support for lambda functions as event callbacks [Closed]

> Username: kylelutz  
> Created at: 2014-04-18 03:16:52 UTC  
> Updated at: 2014-04-19 19:46:07 UTC  
> Closed at: 2014-04-19 19:46:07 UTC  
> Url: https://github.com/boostorg/compute/issues/86  

Add an overload of `event::set_callback()` which takes a lambda function (or event a generic `boost::function<void()>`) which will be called when the event completes.

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-04-19 19:46:07 UTC  
> Url: https://github.com/boostorg/compute/issues/86#issuecomment-40878762  

Implemented in 6ac757887cdb86f44ce75a19405a8e3eb8567e15
