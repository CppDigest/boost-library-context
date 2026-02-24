# #3 Use BOOST_EXPLICIT_OPERATOR_BOOL for optional [Closed]

> Username: MarcelRaad  
> Created at: 2014-04-24 08:02:16 UTC  
> Updated at: 2014-06-17 10:19:21 UTC  
> Closed at: 2014-04-26 13:36:04 UTC  
> Url: https://github.com/boostorg/optional/pull/3  

I often have the problem that when I change a std::wstring to boost::optional<std::wstring> and the variable is used as a parameter with Boost.Format, the result silently changes from the string contents to "1".  
  
This change prevents implicit conversion to bool if the compiler supports explicit conversion operators.

---

## Comment 1

> Username: akrzemi1  
> Created_at: 2014-04-25 07:29:28 UTC  
> Url: https://github.com/boostorg/optional/pull/3#issuecomment-41365877  

Hi Marcel, I just want to let you know that your request didn't go unnoticed. I just need a couple of days to get to the environment where I can test this and update the documentation.  
  
Thanks, &rzej

---

## Comment 2

> Username: akrzemi1  
> Created_at: 2014-04-26 13:36:04 UTC  
> Url: https://github.com/boostorg/optional/pull/3#issuecomment-41468844  

Merged to develop

---
