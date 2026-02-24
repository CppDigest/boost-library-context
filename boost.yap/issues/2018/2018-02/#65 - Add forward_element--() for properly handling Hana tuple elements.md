# #65 - Add forward_element<>() for properly handling Hana tuple elements. [Open]

> Username: tzlaine  
> Created at: 2018-02-25 07:57:02 UTC  
> Updated at: 2018-06-11 17:16:20 UTC  
> Url: https://github.com/boostorg/yap/issues/65  

This should move an element if it is a non-const value; lvalue refs should of course remain lvalue refs as well.
