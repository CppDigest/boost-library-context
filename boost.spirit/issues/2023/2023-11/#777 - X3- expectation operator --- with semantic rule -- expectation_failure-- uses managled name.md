# #777 - X3: expectation operator ">" with semantic rule "[]" expectation_failure<> uses managled name [Open]

> Username: mechapede  
> Created at: 2023-11-23 20:48:56 UTC  
> Updated at: 2025-05-09 22:42:34 UTC  
> Url: https://github.com/boostorg/spirit/issues/777  

The expectation_failure<> failure in Spirit X3 which() function uses the mangled name when semantic actions are used. The minimal example folows below:  
  
  
```C++  
#include <boost/spirit/home/x3.hpp>  
#include <boost/fusion/include/adapt_struct.hpp>  
#include <boost/fusion/include/io.hpp>  
  
#include <iostream>  
#include <string>  
  
  
// if add a get_info struct for semantic actions then get expected token  
#if 0  
  
namespace boost::spirit::x3 {  
    template <typename Derived, typename Action>  
    struct get_info<action<Derived, Action>>  
    {  
        typedef std::string result_type;  
        std::string operator()(action<Derived, Action> const& p) const  
        {  
            return get_info<Derived>()(p.subject);  
        }  
    };  
}  
  
#endif  
  
  
// Simple program to demonstrate expectation behavior with semantic actions  
int main() {  
    namespace x3 = boost::spirit::x3;  
  
    using boost::spirit::x3::ascii::space;  
    typedef std::string::const_iterator iterator_type;  
  
    // invalid input to cause expect to fail  
    std::string input = "aabb";  
  
    // expect alone will print char as expected  
    {  
        auto iter = input.begin();  
        auto const end = input.end();  
        try {  
            bool r = x3::phrase_parse(iter, end,   
                // begin gramar  
                (  
                    x3::char_('a') > x3::char_('b')  
                )  
                ,  
                // end gramar  
            space);  
  
        } catch (x3::expectation_failure<std::string::iterator> const& x) {  
            // catch and print the error object  
            std::cout << "Which :" << x.which() << std::endl;  
        }  
    }  
  
    // expect with semantic action print mangled name  
    {  
        auto semantic_action = [](auto &ctx) {};  
        auto iter = input.begin();  
        auto const end = input.end();  
        try {  
            bool r = x3::phrase_parse(iter, end,   
                // begin gramar  
                (  
                    x3::char_('a') > x3::char_('b')[semantic_action]  
                )  
                ,  
                // end gramar  
            space);  
  
        } catch (x3::expectation_failure<std::string::iterator> const& x) {  
            // catch and print the error object  
            std::cout << "Which with semantic: " << x.which() << std::endl;  
        }  
    }  
  
    return 0;  
}  
  
```  
  
Which outputs the following results:  
```  
Which :'b'  
Which with semantic: N5boost6spirit2x36actionINS1_12literal_charINS0_13char_encoding8standardEcEEZ4mainEUlRT_E_EE  
```  
  
If enable the code block above by chaning to `#if 1` to enable:  
```C++  
namespace boost::spirit::x3 {  
    template <typename Derived, typename Action>  
    struct get_info<action<Derived, Action>>  
    {  
        typedef std::string result_type;  
        std::string operator()(action<Derived, Action> const& p) const  
        {  
            return get_info<Derived>()(p.subject);  
        }  
    };  
}  
```  
  
It adds `get_info` for semantic actions that will print the info for underlying action which is what I expected originally:  
```  
Which :'b'  
Which with semantic: 'b'  
```  
  
I wonder if the X3 library should already include this `get_info<>` specialization or if this causes issues with other use cases?
