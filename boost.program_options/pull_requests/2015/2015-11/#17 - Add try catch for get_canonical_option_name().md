# #17 Add try catch for get_canonical_option_name() [Closed]

> Username: ya1gaurav  
> Created at: 2015-11-03 05:12:19 UTC  
> Updated at: 2017-01-13 19:34:38 UTC  
> Closed at: 2017-01-13 19:34:38 UTC  
> Url: https://github.com/boostorg/program_options/pull/17  

get_canonical_option_name() throws internally as get_canonical_option_prefix() throws as below:  
296         throw std::logic_error("error_with_option_name::m_option_style can only be "  
297                                "one of [0, allow_dash_for_short, allow_slash_for_short, "  
298                                "allow_long_disguise or allow_long]");  
299   
So add try - catch block to disable error reported by static analyzer tool.

---

## Review 1 [Commented]

> Username: vprus  
> Created_at: 2016-12-21 09:56:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/program_options/pull/17#pullrequestreview-13932659  

📁 include/boost/program_options/errors.hpp

```diff
 177 |+         	}
 178 |+         	catch(...)
 179 |+         	{
```

> Username: vprus  
> Created_at: 2016-12-21 09:56:31 UTC  
> Url: https://github.com/boostorg/program_options/pull/17#discussion_r93407310  

I am not quite familiar with using C++ without exceptions - what does above really accomplish? It seems to basically ignore errors, which does not seem reasonable. May I suggest that a better approach is to change get_canonical_option_prefix to use BOOST_THROW?


---

## Comment 2

> Username: vprus  
> Created_at: 2017-01-13 19:34:38 UTC  
> Url: https://github.com/boostorg/program_options/pull/17#issuecomment-272527147  

I'll close this one, at least until/unless the question above is addressed.

---
