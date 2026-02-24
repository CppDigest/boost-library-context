# #497 - environment::current_view doesn't work with Boost.Range [Closed]

> Username: cmorve-te  
> Created at: 2025-06-20 10:35:12 UTC  
> Updated at: 2025-06-20 16:37:09 UTC  
> Closed at: 2025-06-20 16:37:09 UTC  
> Url: https://github.com/boostorg/process/issues/497  

With Boost 1.88  
  
```  
const auto current_environment = boost::process::environment::current();  
boost::range::transform(current_environment,...  
```  
  
this code fails to build.
