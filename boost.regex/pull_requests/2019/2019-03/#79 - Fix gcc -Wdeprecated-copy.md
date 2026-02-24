# #79 Fix gcc -Wdeprecated-copy [Merged]

> Username: stac47  
> Created at: 2019-03-01 08:42:26 UTC  
> Updated at: 2020-01-07 18:54:05 UTC  
> Merged at: 2020-01-07 18:54:05 UTC  
> Closed at: 2020-01-07 18:54:05 UTC  
> Url: https://github.com/boostorg/regex/pull/79  

The goal of this PR is to fix the following warning:  
  
```  
.../include/boost/regex/v4/regex_iterator.hpp:163:22: error: implicitly-declared 'boost::regex_iterator_implementation<const char*, char, boost::regex_traits<char> >::regex_iterator_implementation(const boost::regex_iterator_implementation<const char*, char, boost::regex_traits<char> >&)' is  
 deprecated [-Werror=deprecated-copy]  
  163 |          pdata.reset(new impl(*(pdata.get())));  
      |                      ^~~~~~~~~~~~~~~~~~~~~~~~  
.../include/boost/regex/v4/regex_iterator.hpp:81:35: note: because 'boost::regex_iterator_implementation<const char*, char, boost::regex_traits<char> >' has user-provided 'boost::regex_iterator_implementation<BidirectionalIterator, charT, traits>& boost::regex_iterator_implementation<Bidirec  
tionalIterator, charT, traits>::operator=(const boost::regex_iterator_implementation<BidirectionalIterator, charT, traits>&) [with BidirectionalIterator = const char*; charT = char; traits =  
 boost::regex_traits<char>]'  
   81 |    regex_iterator_implementation& operator=(const regex_iterator_implementation&);  
      |                                   ^~~~~~~~  
cc1plus: all warnings being treated as errors  
```  
This was detected with gcc (GCC) 9.0.1 20190205 (experimental).

---
