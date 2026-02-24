# #111 Fix issue 110: Remove unneeded declaration for MSC [Open]

> Username: yuxianch  
> Created at: 2020-08-28 01:16:19 UTC  
> Updated at: 2020-08-28 01:16:19 UTC  
> Url: https://github.com/boostorg/range/pull/111  

Declaration "template ::boost::counting_iterator;" is not needed on Windows.  
The declaration is also not acceptible with latest clang-cl compiler.

---
