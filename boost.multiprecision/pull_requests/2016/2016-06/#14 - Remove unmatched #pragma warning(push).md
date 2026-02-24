# #14 Remove unmatched #pragma warning(push) [Closed]

> Username: fun4jimmy  
> Created at: 2016-06-01 15:56:08 UTC  
> Updated at: 2016-06-06 08:14:01 UTC  
> Closed at: 2016-06-05 17:32:54 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/14  

The `#pragma warning(push)` in **cpp_int.hpp** did not have a matching `#pragma warning(pop)` which was causing certain warnings to be disabled when including this header. On further investigation removing all disabled warnings entirely seemed to still generate no warnings when including the header so the whole `#ifdef BOOST_MSVC` block was removed. Tested with **Visual Studio 2015 Update 2** using the 64-bit compiler.  
  
The mismatched header was found using the new optional warnings in **Visual Studio 2015 Update 1** C5031 and C5032. See https://msdn.microsoft.com/en-us/library/mt612856.aspx#BK_compiler for more information on those.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2016-06-05 17:32:54 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/14#issuecomment-223826027  

Good catch, I see lots of warnings in the tests though with the pragma's removed, so I've fixed slightly differently in: https://github.com/boostorg/multiprecision/commit/34a06003940269c90eb91228c9e84a1788123ac1

---

## Comment 2

> Username: fun4jimmy  
> Created_at: 2016-06-06 08:14:01 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/14#issuecomment-223894671  

I couldn't run the test machinery myself at work, thanks.

---
