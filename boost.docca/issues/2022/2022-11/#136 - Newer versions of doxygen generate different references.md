# #136 - Newer versions of doxygen generate different references [Closed]

> Username: alandefreitas  
> Created at: 2022-11-08 23:54:41 UTC  
> Updated at: 2023-07-28 20:16:59 UTC  
> Closed at: 2023-07-28 20:16:47 UTC  
> Url: https://github.com/boostorg/docca/issues/136  

Doxygen + docca create different ids when doxygen is updated to a version higher than 1.8.15.  
  
Doxygen 1.9.1 generates:  
  
```xml  
<name>operator*</name>  
```  
  
where docca generates: `url.ref.boost__urls__pct_string_view.operator__star_`.  
  
while doxygen 1.8.5 generates  
  
```xml  
<name>operator *</name>  
```  
  
where docca generates: `url.ref.boost__urls__pct_string_view.operator___star_`  
  
Reference: https://github.com/boostorg/url/pull/611

---

## Comment 1

> Username: sdkrystian  
> Created at: 2023-07-28 20:16:47 UTC  
> Updated at: 2023-07-28 20:16:59 UTC  
> Url: https://github.com/boostorg/docca/issues/136#issuecomment-1656282479  

Fixed by 4b61682742d6fc0d373698d4263502408816006a
