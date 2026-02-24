# #666 - Qi: keyword list parser compilation error with Fusion adapted ADT with BOOST_FUSION_ADAPT_ADT [Closed]

> Username: KuanL1u  
> Created at: 2021-04-19 14:36:17 UTC  
> Updated at: 2021-04-19 19:17:32 UTC  
> Closed at: 2021-04-19 19:17:31 UTC  
> Url: https://github.com/boostorg/spirit/issues/666  

When using the combination of the BOOST_FUSION_ADAPT_ADT macro, and the "kwd" directive from Boost lib, it works fine on WIN(built and tested). However, it would have a compiler error when building on Linux.  
  
Roughly speaking, BOOST_FUSION_ADAPT_ADT ends up creating a sequence whose accessor returns proxy objects (adt_attribute_proxy) that "emulate" by-reference access to the variable in the adapted class (but under the hood, call the get/set methods on the adapted class).  But despite this, this object really has "value" semantics as far as the language is concerned.  But the "variant visitor class which handles dispatching the parsing to the selected parser" (boost/spirit/repository/home/qi/operator/detail/keywords.hpp in v1.75) passes the return value of the sequence access function as an argument to a function that requires the argument by reference.  
  
  
This isn't really supported by C++, but it works on MSVC because MSVC supports a language extension that permits passing a temporary variable by non-const lvalue reference.  
  
Please see the **demo code** and **error message** below.  
  
```  
#include <boost/fusion/adapted.hpp>  
#include <boost/spirit/home/qi.hpp>  
  
#include <boost/spirit/include/qi.hpp>  
#include <boost/spirit/include/phoenix_core.hpp>  
#include <boost/spirit/include/phoenix_object.hpp>  
#include <boost/spirit/include/phoenix_operator.hpp>  
#include <boost/spirit/include/phoenix_fusion.hpp>  
#include <boost/fusion/include/adapt_struct.hpp>  
#include <boost/spirit/include/support_adapt_adt_attributes.hpp>  
#include <boost/spirit/include/support_attributes.hpp>  
#include <boost/fusion/include/adapt_adt.hpp>  
#include <boost/fusion/include/sequence.hpp>  
#include <boost/spirit/repository/include/qi_kwd.hpp>  
#include <boost/spirit/repository/include/qi_keywords.hpp>  
#include <iostream>  
#include <string>  
#include <cstdlib>  
#include <iterator>  
  
struct person {  
    int age1;  
    int age2;  
         
   const int& getAge1(void) const {  
    return age1;  
   }  
   const int& getAge2(void) const {  
    return age2;  
   }  
   void setAge1(const int &val) {  
    age1 = val;  
   }  
   void setAge2(const int &val) {  
    age2 = val;  
   }  
};  
  
  
BOOST_FUSION_ADAPT_ADT( person,  
    (obj.getAge1(), obj.setAge1(val))  
    (obj.getAge2(), obj.setAge2(val))  
)  
  
/*  
BOOST_FUSION_ADAPT_STRUCT( person,  
    age1,  
    age2  
)  
*/  
  
  
int main()  
{  
    // keyword_list  
    {  
        //[reference_using_declarations_keyword_list  
        using boost::spirit::repository::qi::kwd;  
        using boost::spirit::qi::inf;  
        using boost::spirit::ascii::space_type;   
        using boost::spirit::ascii::char_;  
        using boost::spirit::qi::double_;  
        using boost::spirit::qi::int_;  
        using boost::spirit::qi::rule;  
        //]  
         
        //[reference_keyword_list_rule_declarations  
        rule<const char *, person(), space_type> no_constraint_person_rule;   
        //]  
          
        //[reference_keyword_list_no_constraint_rule  
        no_constraint_person_rule %=   
            kwd("age1")   [int_]   
          / kwd("age2")   [int_]   
          ;  
        //]  
  
    }          
    return 0;  
}  
  
```  
  
![Capture](https://user-images.githubusercontent.com/22803967/115252023-171cdb00-a0f9-11eb-9ddf-99d9b8b49091.PNG)  
  
  
**Possible fix** (Thanks Ed for providing it):  
  
replacing in the function `bool call_subject( ...` in  
  
`boost/spirit/repository/home/qi/operator/detail/keywords.hpp`  
  
The line:  
  
`if(subject.parse(first,last,context,marked_skipper,fusion::at_c<Index::value>(attr)))`  
  
with two lines:  
  
```  
decltype(fusion::at_c<Index::value>(attr)) attrElement = fusion::at_c<Index::value>(attr);                      
if(subject.parse(first,last,context,marked_skipper,attrElement))  
```  
  
Then in the case that was failing, we have an lvalue that can be freely passed by reference.  
1.	If fusion::at_c<Index::value>(attr) is a reference (common usage), then  
        a.	decltype(fusion::at_c<Index::value>(attr)) attrElement will be a reference type, so no functional change  
2.	If fusion::at_c<Index::value>(attr) is an rvalue (problematic case), then  
        a.	decltype(fusion::at_c<Index::value>(attr)) attrElement will take a copy of the rvalue.  
  
This makes the failure reproduction case pass, and passes the boost regression tests (using "auto" instead of "decltype" will not because chains of references get broken), but to be honest, I am not sufficiently expert in template magic to see if this would have other catastrophic unintended consequences.

---

## Comment 1

> Username: Kojoley  
> Created at: 2021-04-19 17:27:03 UTC  
> Url: https://github.com/boostorg/spirit/issues/666#issuecomment-822643539  

I confirm the bug.  
  
A general advices:  
* do not use Fusion adapted ADT, it is not fully supported (container values are not supported at all)  
* do not use `keyword` parser until you can prove it actually speeds up your parser, it definitely slows down your compile times  
  
> ...  
> using "auto" instead of "decltype" will not because chains of references get broken  
  
That's what `decltype(auto)` was added for in C++14, but the library is C++03.
