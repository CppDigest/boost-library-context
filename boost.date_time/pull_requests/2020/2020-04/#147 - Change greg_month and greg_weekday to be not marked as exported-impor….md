# #147 Change greg_month and greg_weekday to be not marked as exported/impor… [Merged]

> Username: JeffGarland  
> Created at: 2020-04-06 17:02:23 UTC  
> Updated at: 2020-04-06 17:02:52 UTC  
> Merged at: 2020-04-06 17:02:52 UTC  
> Closed at: 2020-04-06 17:02:52 UTC  
> Url: https://github.com/boostorg/date_time/pull/147  

…ted (#146)  
  
* Changed greg_month and greg_weekday to be not marked as exported/imported.  
  
Both greg_month and greg_weekday classes are implemented completely in headers,  
so they need not be marked with BOOST_DATE_TIME_DECL. For consistency with other  
similar classes, they are now marked with BOOST_SYMBOL_VISIBLE.  
  
This should fix linking errors on Windows/MSVC, where all members of greg_month  
and greg_weekday classes remain unresolved as they are expected to be  
implemented in a shared library.  
  
* Enabled shared and static linking in AppVeyor CI.

---
