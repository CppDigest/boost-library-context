# #246 - Outcome explicit-failures-markup.xml not getting picked up [Closed]

> Username: ned14  
> Created at: 2019-01-30 15:45:24 UTC  
> Updated at: 2019-01-30 22:36:48 UTC  
> Closed at: 2019-01-30 22:36:48 UTC  
> Url: https://github.com/boostorg/boost/issues/246  

As evidenced by https://www.boost.org/development/tests/master/developer/outcome.html, my https://github.com/boostorg/outcome/blob/master/meta/explicit-failures-markup.xml appears to not be picked up.  
  
Also, on https://www.boost.org/doc/libs/develop, Outcome's categories are not being picked up, nor is the C++ standard required being found. I believe my https://github.com/boostorg/outcome/blob/master/meta/libraries.json is correct.  
  
Note that the categories and standard required problem seems to affect more libraries than just Outcome. Or rather, I would have thought many of the libraries in Miscellaneous category ought to not be there. And libraries such as Beast and Hana should be C++ 11 and C++ 14 standard required.

---

## Comment 1

> Username: ned14  
> Created at: 2019-01-30 15:45:58 UTC  
> Url: https://github.com/boostorg/boost/issues/246#issuecomment-458992994  

@danieljames Peter suggested I loop you in.

---

## Comment 2

> Username: danieljames  
> Created at: 2019-01-30 21:59:05 UTC  
> Url: https://github.com/boostorg/boost/issues/246#issuecomment-459126326  

xmllint is rejecting it:  
  
    explicit-failures-markup.xml:24: element toolset: Schemas validity error : Element 'toolset': This element is not expected. Expected is ( test ).  
  
Your `<mark-expected-failures>` doesn't specify which tests will fail. If it's all of them, you need to use `<mark-unusable>`.  
  
For libraries.json, you need to use the keywords from [the documentation](https://www.boost.org/development/library_metadata.html), like `Patterns`. The `std` field is not which standards you require, but which standards your library is included in.

---

## Comment 3

> Username: ned14  
> Created at: 2019-01-30 22:36:48 UTC  
> Url: https://github.com/boostorg/boost/issues/246#issuecomment-459138040  

Ok, both files are modified according to your notes. Let's see what happens. Thanks for the help.
