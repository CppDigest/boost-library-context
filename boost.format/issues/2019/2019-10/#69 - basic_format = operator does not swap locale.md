# #69 - basic_format = operator does not swap locale [Closed]

> Username: tancaj  
> Created at: 2019-10-09 15:57:55 UTC  
> Updated at: 2021-02-02 12:35:12 UTC  
> Closed at: 2021-02-02 12:35:12 UTC  
> Url: https://github.com/boostorg/format/issues/69  

Tracking down a locale related bug in my source code, I found that the = operator of basic_format doesn't swap its locale, though the copy constructor does copy it. I find it confusing and hard to use this way, if i want to use a basic_format as a member and change it according to different needs.  
  
https://github.com/boostorg/format/blob/894f465d843d999b5c024a49f90a5a1013edd368/include/boost/format/format_implementation.hpp#L67-L95
