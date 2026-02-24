# #12 Add operator<< for boost::none [Merged]

> Username: jhunold  
> Created at: 2014-11-24 14:42:47 UTC  
> Updated at: 2014-11-24 14:46:33 UTC  
> Merged at: 2014-11-24 14:46:33 UTC  
> Closed at: 2014-11-24 14:46:33 UTC  
> Url: https://github.com/boostorg/optional/pull/12  

The new definition of none_t inhibits conversion to nullptr.This leads to errors if none is output to a stream, like Boost.Test diagnostics output.  
Add the missing operator plus testcase.

---
