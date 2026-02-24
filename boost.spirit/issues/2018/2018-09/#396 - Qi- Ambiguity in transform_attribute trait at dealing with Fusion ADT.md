# #396 - Qi: Ambiguity in transform_attribute trait at dealing with Fusion ADT [Closed]

> Username: Kojoley  
> Created at: 2018-09-08 00:11:52 UTC  
> Updated at: 2018-10-23 02:10:48 UTC  
> Closed at: 2018-10-23 02:10:48 UTC  
> Url: https://github.com/boostorg/spirit/issues/396  

```cpp  
#include <boost/spirit/include/qi.hpp>  
#include <boost/spirit/include/support_adapt_adt_attributes.hpp>  
  
struct Vec2i  
{  
    int x;  
    int y;  
};  
  
BOOST_FUSION_ADAPT_ADT(  
    Vec2i,  
    (int, int, obj.x, obj.x = val)  
    (int, int, obj.y, obj.y = val)  
)  
  
int main()  
{  
    namespace qi = boost::spirit::qi;  
  
    qi::rule<char const*, Vec2i()> v1 = qi::uint_ >> ',' >> qi::uint_; // OK  
  
    qi::rule<char const*, int()> coord = qi::uint_;  
    qi::rule<char const*, Vec2i()> v2 = coord >> ',' >> coord; // Fails to compile  
}  
```  
  
```  
boost\spirit\home\qi\detail\attributes.hpp(154): error C2752: 'boost::spirit::traits::transform_attribute<Exposed,Transformed,boost::spirit::qi::domain,void>': more than one partial specialization matches the template argument list  
        with  
        [  
            Exposed=boost::fusion::extension::adt_attribute_proxy<Vec2i,0,false>,  
            Transformed=int  
        ]  
boost\spirit\home\qi\detail\attributes.hpp(154): note: could be 'boost::spirit::traits::transform_attribute<Exposed,Transformed,boost::spirit::qi::domain,void>'  
boost\spirit\home\qi\detail\attributes.hpp(154): note: or       'boost::spirit::traits::transform_attribute<boost::fusion::extension::adt_attribute_proxy<T,N,false>,Attribute,Domain,disable_if<boost::is_reference<fusion::extension::adt_attribute_proxy<T,N,false>::type>,void>::type>'  
  
```
