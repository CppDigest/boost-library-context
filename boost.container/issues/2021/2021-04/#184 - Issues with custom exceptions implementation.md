# #184 - Issues with custom exceptions implementation [Closed]

> Username: igaztanaga  
> Created at: 2021-04-19 16:00:50 UTC  
> Updated at: 2021-04-19 19:53:02 UTC  
> Closed at: 2021-04-19 19:53:02 UTC  
> Url: https://github.com/boostorg/container/issues/184  

As reported in issue #183, there are implementation issues:  
  
- Needless #ifdef BOOST_CONTAINER_USE_STD_EXCEPTIONS checks in the throw_* functions  
- Likely visibility issues when throwing/catching such exceptions over DSO boundaries due to -fvisibility=hidden being the default (now) and no BOOST_SYMBOL_EXPORT or similar on the class  
- docs were forgotten to be updated: <li>If BOOST_NO_EXCEPTIONS is NOT defined <code>std::logic_error(str)</code> is thrown.</li>
