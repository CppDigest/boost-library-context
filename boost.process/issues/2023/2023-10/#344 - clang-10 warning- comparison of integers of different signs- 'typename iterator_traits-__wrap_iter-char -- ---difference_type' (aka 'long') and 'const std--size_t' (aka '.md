# #344 - clang-10 warning: comparison of integers of different signs: 'typename iterator_traits<__wrap_iter<char *> >::difference_type' (aka 'long') and 'const std::size_t' (aka 'const unsigned long') [Open]

> Username: k15tfu  
> Created at: 2023-10-05 16:45:40 UTC  
> Updated at: 2023-10-05 16:45:40 UTC  
> Url: https://github.com/boostorg/process/issues/344  

Hi!  
  
In file boost/process/environment.hpp:  
```  
boost/process/environment.hpp:267:56: error: comparison of integers of different signs: 'typename iterator_traits<__wrap_iter<char *> >::difference_type' (aka 'long') and 'const std::size_t' (aka 'const unsigned long') [-Werror,-Wsign-compare]  
            if ((std::distance(st1.begin(), st1.end()) < len)  
                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ ^ ~~~  
[...]  
1 error generated.  
```  
  
Linked issue: https://github.com/boostorg/process/issues/267
