# #42 noexcept is_empty throws on missing permissions. [Closed]

> Username: ghost  
> Created at: 2017-03-22 20:32:36 UTC  
> Updated at: 2017-04-03 13:54:09 UTC  
> Closed at: 2017-04-03 13:54:09 UTC  
> Url: https://github.com/boostorg/filesystem/pull/42  

`boost::filesystem::is_emtpy` with `boost::system::error_code` throws on missing permissions.

---

## Comment 1

> Username: Beman  
> Created_at: 2017-04-03 13:54:09 UTC  
> Url: https://github.com/boostorg/filesystem/pull/42#issuecomment-291149870  

The editor used for the P/R removed trailing spaces from lines. That causes unwanted churn. So I made the changes by hand.  
  
Thanks for the P/R!  
  
--Beman

---
