# #29 - Parse additional ISO C99 format string specifiers [Closed]

> Username: jeking3  
> Created at: 2017-10-18 14:02:13 UTC  
> Updated at: 2017-12-18 15:35:28 UTC  
> Closed at: 2017-10-24 19:50:08 UTC  
> Url: https://github.com/boostorg/format/issues/29  

### Argument Types ###  
  
Per the Boost.Format documentation, argument types are ignored, however there are some newer ones that appeared in ISO C99 which should be parsed at least so that an exception is not thrown:  
  
Currently, using `'hh'`, `'h'`, `'l'`, `'ll'`, and `'L'` parse properly, however using `'j'` or `'z'`, cause a `boost::io::bad_format_string`.  
  
The argument type specifier `'t'` from C++11 cannot be honored by boost::format because `'T'` and `'t'` are used in boost::format for tabulations.
