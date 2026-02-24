# #249 - C++20 breaks broken print_log_value<wchar_t const*>::operator() even more [Closed]

> Username: CaseyCarter  
> Created at: 2020-01-30 21:52:12 UTC  
> Updated at: 2020-04-21 14:23:38 UTC  
> Closed at: 2020-03-29 16:30:46 UTC  
> Url: https://github.com/boostorg/test/issues/249  

`print_log_value<wchar_t const*>::operator()`:  
  
https://github.com/boostorg/test/blob/119a3a72941065b84fe1de0fb5a29aa29694d48c/include/boost/test/impl/test_tools.ipp#L124-L128  
  
is completely bogus. Narrow streams (`std::ostream& ostr`) have no special support for pointer-to-`wchar_t`, so in C++17 and earlier the result is to call the `void*` stream insertion operator and output the pointer value rather than the characters it points to.  
  
C++20 helpfully renders this function ill-formed by adding a deleted stream insertion operator for `std::ostream` and `wchar_t const*`; see http://eel.is/c++draft/diff.cpp17.input.output#3.  
  
I'm not sure why this function exists, but I suspect it is not fit for purpose.

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2020-03-29 16:30:46 UTC  
> Url: https://github.com/boostorg/test/issues/249#issuecomment-605662977  

Fixed via #252
