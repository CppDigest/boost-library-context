# #165 Don't forward declare struct tst_pass_through [Merged]

> Username: AMDmi3  
> Created at: 2015-11-16 14:19:58 UTC  
> Updated at: 2015-11-16 23:22:22 UTC  
> Merged at: 2015-11-16 22:25:52 UTC  
> Closed at: 2015-11-16 22:25:52 UTC  
> Url: https://github.com/boostorg/spirit/pull/165  

It is used later in find(), so complete type is needed. Include tst.hpp which defines it.  
  
Test program:  
  
```  
#include <boost/spirit/home/qi/string/tst_map.hpp>  
  
int main() {  
    boost::spirit::qi::tst_map<char, int> map;  
    int* x;  
    map.find(x, x);  
    return 0;  
}  
```  
  
Clang output:  
  
```  
In file included from test.cc:1:  
/usr/local/include/boost/spirit/home/qi/string/tst_map.hpp:74:38: error: invalid use of incomplete type 'boost::spirit::qi::tst_pass_through'  
            return find(first, last, tst_pass_through());  
                                     ^~~~~~~~~~~~~~~~~~  
/usr/local/include/boost/spirit/home/qi/string/tst_map.hpp:20:12: note: forward declaration of 'boost::spirit::qi::tst_pass_through'  
    struct tst_pass_through; // declared in tst.hpp  
           ^  
1 error generated.  
```  
  
gcc output:  
  
```  
In file included from test.cc:1:0:  
/usr/local/include/boost/spirit/home/qi/string/tst_map.hpp: In instantiation of 'T* boost::spirit::qi::tst_map<Char, T>::find(Iterator&, Iterator) const [with Iterator = int*; Char = char; T = int]':  
test.cc:6:15:   required from here  
/usr/local/include/boost/spirit/home/qi/string/tst_map.hpp:74:56: error: invalid use of incomplete type 'struct boost::spirit::qi::tst_pass_through'  
             return find(first, last, tst_pass_through());  
                                                        ^  
/usr/local/include/boost/spirit/home/qi/string/tst_map.hpp:20:12: error: forward declaration of 'struct boost::spirit::qi::tst_pass_through'  
     struct tst_pass_through; // declared in tst.hpp  
            ^  
```

---

## Comment 1

> Username: AMDmi3  
> Created_at: 2015-11-16 23:22:19 UTC  
> Url: https://github.com/boostorg/spirit/pull/165#issuecomment-157206371  

Thank you!  
  
For reference, here's a link to corresponding trac issue:  
  
https://svn.boost.org/trac/boost/ticket/9468

---
