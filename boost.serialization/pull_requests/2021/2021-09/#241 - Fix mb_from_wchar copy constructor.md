# #241 Fix mb_from_wchar copy constructor [Merged]

> Username: alxrmorozov  
> Created at: 2021-09-13 22:22:58 UTC  
> Updated at: 2022-09-15 20:43:29 UTC  
> Merged at: 2022-09-15 20:43:28 UTC  
> Closed at: 2022-09-15 20:43:28 UTC  
> Url: https://github.com/boostorg/serialization/pull/241  

Now mb_from_wchar copy constructor does not copy all fields so a copy can work incorrectly.  
If m_full is true after copying dereference_impl() will return garbage from m_buffer.  
A problem is found with Svace static analyzer. It warned that m_mbs is not initialized in a constructor.  
  
m_codecvt_facet is not copied but this is not a problem because all its methods are const.

---
