# #103 - More details are needed in the boost::too_many_args [Open]

> Username: ujos  
> Created at: 2024-11-14 11:32:49 UTC  
> Updated at: 2024-11-14 11:32:49 UTC  
> Url: https://github.com/boostorg/format/issues/103  

Could you add the format string caused the issue to the `boost::too_many_args` exception text?  
  
I've got the following error message in the log file and cannot find where does it come from:  
```  
boost::too_many_args: format-string referred to fewer arguments than were passed  
```  
  
In case if exception text contain the format string associated with the given event, that would help a lot.  
  
---  
Boost: v1.86
