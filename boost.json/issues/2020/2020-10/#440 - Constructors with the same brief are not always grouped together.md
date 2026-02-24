# #440 - Constructors with the same brief are not always grouped together [Closed]

> Username: vinniefalco  
> Created at: 2020-10-04 01:58:28 UTC  
> Updated at: 2020-10-16 05:42:06 UTC  
> Closed at: 2020-10-16 05:42:05 UTC  
> Url: https://github.com/boostorg/json/issues/440  

![image](https://user-images.githubusercontent.com/1503976/95005229-67dc4480-05aa-11eb-83d2-976404f54833.png)

---

## Comment 1

> Username: evanlenz  
> Created at: 2020-10-09 06:24:49 UTC  
> Updated at: 2020-10-09 06:26:13 UTC  
> Url: https://github.com/boostorg/json/issues/440#issuecomment-705994712  

It looks like the Doxygen output has the memberdefs grouped into three separate <sectiondef> elements:  
  
```xml  
<sectiondef kind="user-defined">...</sectiondef>  
<sectiondef kind="user-defined">...</sectiondef>  
<sectiondef kind="public">...</sectiondef>  
```  
  
Do you know what causes these? The current XSLT only de-duplicates briefdescription values within the sectiondef, not across sectiondefs. It sounds like we may want to essentially ignore the sectiondefs for purposes of rendering the overload-list page.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-10-16 05:42:05 UTC  
> Url: https://github.com/boostorg/json/issues/440#issuecomment-709805202  

Moved to https://github.com/boostorg/docca/issues/44
