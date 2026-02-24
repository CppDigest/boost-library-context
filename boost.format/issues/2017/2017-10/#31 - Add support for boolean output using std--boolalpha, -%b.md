# #31 - Add support for boolean output using std::boolalpha, "%b" [Closed]

> Username: jeking3  
> Created at: 2017-10-18 14:15:25 UTC  
> Updated at: 2017-12-18 15:36:00 UTC  
> Closed at: 2017-10-24 20:28:13 UTC  
> Url: https://github.com/boostorg/format/issues/31  

### Proposal ###  
  
Per the original idea found in [Francis (Grizzly) Smit's Boost Trac issue](https://svn.boost.org/trac10/ticket/4659), add support for conversion specifier `'b'` that shall map to `std::boolalpha`.  Given that `std::uppercase` only applies to numeric inputs, there will be no `'B'` conversion specifier support; instead one can follow this documentation to customize the boolean string output:  
  
http://en.cppreference.com/w/cpp/locale/numpunct/truefalsename
