# #4 - Variant files shouldn't be installed when there's no lib-target [Closed]

> Username: pdimov  
> Created at: 2019-04-17 21:16:42 UTC  
> Updated at: 2019-04-18 15:43:15 UTC  
> Closed at: 2019-04-18 15:43:14 UTC  
> Url: https://github.com/boostorg/boost_install/issues/4  

In the `generate-cmake-variant` rule, when `$(lib-target)` is empty, we still generate a variant.cmake file, which is undesirable.  
  
I've tried to fix that in https://github.com/boostorg/boost_install/commit/4c2bc82d4bd3ebc4699b67efe522067b63d9e34b, but this results in b2 warnings of the form  
  
```  
warning: Unable to construct libs/stacktrace/build/boost_stacktrace_backtrace-variant.cmake  
```  
  
(happens because `boost_stacktrace_backtrace` is disabled with `check-target-builds`.)  
  
@swatanabe, do you have any ideas how these messages can be avoided? Perhaps I need to return some kind of a dummy target from the rule?

---

## Comment 1

> Username: swatanabe  
> Created at: 2019-04-17 21:40:12 UTC  
> Url: https://github.com/boostorg/boost_install/issues/4#issuecomment-484271758  

AMDG  
  
On 4/17/19 3:16 PM, Peter Dimov wrote:  
>   
> ```  
> warning: Unable to construct libs/stacktrace/build/boost_stacktrace_backtrace-variant.cmake  
> ```  
>   
> (happens because `boost_stacktrace_backtrace` is disabled with `check-target-builds`.)  
>   
> @swatanabe, do you have any ideas how these messages can be avoided? Perhaps I need to return some kind of a dummy target from the rule?  
>   
  
The result of generate is an optional property-set  
(the usage-requirements) followed by a list of targets.  
If there are no targets, then you must include the  
property-set to distinguish a successful result with  
no targets from a failure.  This seems to be a special  
case in generate, and I'm not sure that the warning  
is actually useful.  
  
return [ property-set.empty ] $(result) ;  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: pdimov  
> Created at: 2019-04-18 15:43:14 UTC  
> Url: https://github.com/boostorg/boost_install/issues/4#issuecomment-484566945  

Perfect, thanks.
