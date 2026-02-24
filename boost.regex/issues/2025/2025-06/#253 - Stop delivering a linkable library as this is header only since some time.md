# #253 - Stop delivering a linkable library as this is header only since some time [Closed]

> Username: Romain-Geissler-1A  
> Created at: 2025-06-26 15:58:13 UTC  
> Updated at: 2025-07-04 22:52:19 UTC  
> Closed at: 2025-07-04 22:52:19 UTC  
> Url: https://github.com/boostorg/regex/issues/253  

Hi,  
  
Similarly to https://github.com/boostorg/system/issues/132, since the doc mentions that Boost.Regex is header only except for the ones needing the deprecated POSIX API, would it make sense nowadays to drop the deprecated POSIX API entirely and stop building any linkable library libboost_regex.so/a ?  
  
Thanks,  
Romain

---

## Comment 1

> Username: Romain-Geissler-1A  
> Created at: 2025-07-04 22:52:19 UTC  
> Url: https://github.com/boostorg/regex/issues/253#issuecomment-3037359304  

Implemented via https://github.com/boostorg/regex/pull/256
