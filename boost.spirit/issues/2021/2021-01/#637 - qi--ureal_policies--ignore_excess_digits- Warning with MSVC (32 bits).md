# #637 - qi::ureal_policies::ignore_excess_digits: Warning with MSVC (32 bits) [Closed]

> Username: stefancat  
> Created at: 2021-01-12 09:30:20 UTC  
> Updated at: 2021-02-27 19:10:25 UTC  
> Closed at: 2021-02-27 19:10:25 UTC  
> Url: https://github.com/boostorg/spirit/issues/637  

Here is how to trigger the warning:  
  
```  
#include <boost/spirit/include/qi.hpp>  
  
using namespace boost::spirit;  
  
int main()  
{  
  qi::rule<istream_iterator, float> r = float_;  
  return 0;  
}  
```  
  
The warning being:  
`boost/spirit/home/qi/numeric/real_policies.hpp(82): warning C4244: 'return': conversion from '__int64' to 'size_t', possible loss of data.`  
  
And my setup:  
- Boost 1.75  
- MSVC 2017 15.9.29 (Win32)  
  
I took a quick look at the code:  
https://github.com/boostorg/spirit/blob/21860d04329d89f8aac95f8fad5951854c6fa686/include/boost/spirit/home/qi/numeric/real_policies.hpp#L75-L84  
  
In this case, `std::distance`'s return type is `std::iterator_traits<Iterator>::difference_type` which is `  
__int64` (`signed long long`), as opposed to `std::size_t` which is `unsigned int` (with 32 bits on MSCV 2017).
