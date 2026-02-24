# #32 Bugfix/raw handles container [Merged]

> Username: vtnerd  
> Created at: 2014-05-30 12:58:04 UTC  
> Updated at: 2014-06-26 23:24:16 UTC  
> Merged at: 2014-05-31 05:32:18 UTC  
> Closed at: 2014-05-31 05:32:18 UTC  
> Url: https://github.com/boostorg/spirit/pull/32  

I think you wanted handles_container for the raw directive to return mpl::false_, but I thought differently (it wouldn't work without changes to the sequence operator). Please read the lengthy commit message, it explains most of it. I also added a few more tests, which hopefully remove any doubt for this decision.  
  
This probably isn't the right place but - changing is_container to return false_ with the iterator_range type seems iffy for spirit x2. I'm going to investigate its usage more, but its likely that the investigation will result in recommendations for x3 but no changes for x2.

---
