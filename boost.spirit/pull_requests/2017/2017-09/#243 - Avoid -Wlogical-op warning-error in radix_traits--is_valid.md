# #243 Avoid -Wlogical-op warning/error in radix_traits::is_valid [Merged]

> Username: ecatmur  
> Created at: 2017-09-19 17:52:08 UTC  
> Updated at: 2017-09-20 02:37:08 UTC  
> Merged at: 2017-09-20 02:37:08 UTC  
> Closed at: 2017-09-20 02:37:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/243  

With -Wlogical-op, gcc warns in radix_traits::is_valid with Radix <= 10:  
  
```  
boost/spirit/home/qi/numeric/detail/numeric_utils.hpp: In instantiation of ‘static bool boost::spirit::qi::detail::radix_traits<10u>::is_valid<char>(char)’:  
[...]  
boost/spirit/home/qi/numeric/detail/numeric_utils.hpp:107:31: warning: logical ‘and’ of mutually exclusive tests is always false [-Wlogical-op]  
                 || (ch >= 'a' && ch <= static_cast<Char>('a' + Radix -10 -1))  
                               ^  
boost/spirit/home/qi/numeric/detail/numeric_utils.hpp:108:31: warning: logical ‘and’ of mutually exclusive tests is always false [-Wlogical-op]  
                 || (ch >= 'A' && ch <= static_cast<Char>('A' + Radix -10 -1));  
                               ^  
```  
  
Rather than suppressing the warning (https://github.com/boostorg/spirit/pull/77), refactor the test so that the warning is not triggered.

---
