# #201 - total_seconds() reports incorrect results [Closed]

> Username: cschadl  
> Created at: 2021-12-14 17:54:54 UTC  
> Updated at: 2021-12-15 15:57:18 UTC  
> Closed at: 2021-12-15 15:57:18 UTC  
> Url: https://github.com/boostorg/date_time/issues/201  

```  
boost::posix_time::ptime date(boost::posix_time::from_iso_extended_string("2021-04-22T18:05:44.443"));  
boost::posix_time::ptime epoch(boost::gregorian::date(1970, 1, 1));  
std::time_t unix_time = (date - epoch).total_seconds();  
```  
With this code, `unix_time` is `1619114748`.  That doesn't appear to be correct (it's about 4 seconds off), if I turn that date into a UNIX timestamp in e.g. PowerShell here is what I get:  
```  
PS > $ts = New-TimeSpan -Start (Get-Date "1970-01-01") -End (Get-Date "2021-04-22T18:05:44.443")  
PS > $ts.TotalSeconds  
1619114744.443  
```  
  
If I assign `unix_time` like this, I do get the correct UNIX timestamp:  
```  
std::time_t unix_time = (date - epoch).ticks() / boost::posix_time::time_duration::rep_type::ticks_per_second;  
```  
  
Is this a bug, or am I just misunderstanding something about how `total_seconds()` works?

---

## Comment 1

> Username: cschadl  
> Created at: 2021-12-15 15:57:18 UTC  
> Url: https://github.com/boostorg/date_time/issues/201#issuecomment-994926142  

Ah, never mind, this turned out to be an issue in my code.  Sorry about that...
