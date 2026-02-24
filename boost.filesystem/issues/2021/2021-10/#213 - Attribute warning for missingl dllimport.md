# #213 - Attribute warning for missingl dllimport [Closed]

> Username: Flamefire  
> Created at: 2021-10-26 14:13:10 UTC  
> Updated at: 2021-10-26 16:51:47 UTC  
> Closed at: 2021-10-26 16:50:53 UTC  
> Url: https://github.com/boostorg/filesystem/issues/213  

https://github.com/boostorg/filesystem/commit/3a323cae2d8d71448c3a65af2d3d5e479d6d3da2 introduced a friend declaration for lex_compare_v3 which now throws warnings:  
  
```  
./boost/filesystem/path.hpp:745:16: error: 'int boost::filesystem::detail::lex_compare_v3(boost::filesystem::path::iterator, boost::filesystem::path::iterator, boost::filesystem::path::iterator, boost::filesystem::path::iterator)' redeclared without dllimport attribute: previous dllimport ignored [-Werror=attributes]  
     friend int detail::lex_compare_v3(path::iterator first1, path::iterator last1, path::iterator first2, path::iterator last2);  
                ^~~~~~  
```  
  
Could you maybe add CI with warnings-as-errors enabled to catch things like that?

---

## Comment 1

> Username: Lastique  
> Created at: 2021-10-26 16:51:46 UTC  
> Url: https://github.com/boostorg/filesystem/issues/213#issuecomment-952127344  

Thanks.  
  
> Could you maybe add CI with warnings-as-errors enabled to catch things like that?  
  
I'm not supporting warnings as errors.
