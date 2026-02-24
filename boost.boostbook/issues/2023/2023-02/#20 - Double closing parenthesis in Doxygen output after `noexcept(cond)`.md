# #20 - Double closing parenthesis in Doxygen output after `noexcept(cond)` [Closed]

> Username: Lastique  
> Created at: 2023-02-26 00:59:47 UTC  
> Updated at: 2023-02-26 13:06:33 UTC  
> Closed at: 2023-02-26 13:06:33 UTC  
> Url: https://github.com/boostorg/boostbook/issues/20  

BoostBook XSLT generate a double closing parenthesis after `noexcept(cond)` in Doxygen-generated documentation. For example, if you run Doxygen over this code:  
  
```  
struct foo  
{  
    void method() noexcept(cond);  
};  
```  
  
and then apply BoostBook XSLTs, the generated documentation will have the method signature as `void method() noexcept(cond))` (note the double closing parenthesis at the end). Here's a [real world example](https://www.boost.org/doc/libs/1_81_0/doc/html/boost/intrusive/hashdata_internal.html#idm32877-bb).  
  
I suspect, the problematic rules are [these](https://github.com/boostorg/boostbook/blob/8ea26491df2a2378fd13db8699c45ad0d50e8295/xsl/doxygen/doxygen2boostbook.xsl#L1146-L1216).
