# #316 Parse into range [Merged]

> Username: octopus-prime  
> Created at: 2017-12-06 18:29:28 UTC  
> Updated at: 2017-12-07 12:17:44 UTC  
> Merged at: 2017-12-07 10:57:57 UTC  
> Closed at: 2017-12-07 10:57:57 UTC  
> Url: https://github.com/boostorg/spirit/pull/316  

See https://svn.boost.org/trac10/ticket/12928  
  
```cpp  
    std::string str("x123x");   
    boost::iterator_range<boost::range_iterator<decltype(str)>::type> attr;   
    if( x3::parse( boost::begin(str), boost::end(str), x3::lit('x') >> x3::raw[+x3::digit] >> x3::lit('x'), attr ) ) {   
        std::cout<<"Match! attr = "<<attr<<std::endl;   
    } else {   
        std::cout<<"Not match!"<<std::endl;   
    }  
```  
produces compile error  
```  
/usr/local/include/boost/spirit/home/x3/support/traits/container_traits.hpp:176:15: error: no member named 'insert' in 'boost::iterator_range<__gnu_cxx::__normal_iterator<char *, std::__cxx11::basic_string<char> > >'  
  
            c.insert(c.end(), first, last);  
  
            ~ ^  
```  
The iterator_range is no container and must not go to container_traits.

---

## Comment 1

> Username: djowel  
> Created_at: 2017-12-07 06:46:41 UTC  
> Url: https://github.com/boostorg/spirit/pull/316#issuecomment-349878591  

Absolutely!

---

## Comment 2

> Username: wanghan02  
> Created_at: 2017-12-07 10:27:13 UTC  
> Url: https://github.com/boostorg/spirit/pull/316#issuecomment-349926528  

Great!

---
