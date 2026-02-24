# #7 [winrt support] Not defining BOOST_INTRUSIVE_TT_TEST_MSC_FUNC_SIGS if targeting ARM on Windows [Merged]

> Username: stgates  
> Created at: 2014-05-06 18:59:04 UTC  
> Updated at: 2014-06-04 17:24:35 UTC  
> Merged at: 2014-05-11 12:25:40 UTC  
> Closed at: 2014-05-11 12:25:40 UTC  
> Url: https://github.com/boostorg/intrusive/pull/7  

Making sure BOOST_INTRUSIVE_TT_TEST_MSC_FUNC_SIGS is not defined if the macro _M_ARM is defined.  
  
This is part of the work mentioned on the Boost dev mailing list:  
  
http://boost.2283326.n4.nabble.com/winrt-support-Adding-support-for-Windows-8-store-phone-to-Boost-libraries-tc4661713.html

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2014-05-11 12:25:59 UTC  
> Url: https://github.com/boostorg/intrusive/pull/7#issuecomment-42769250  

Thanks for the patch

---
