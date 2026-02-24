# #78 - Function name sometimes contains null character [Closed]

> Username: bluescarni  
> Created at: 2019-06-10 15:11:11 UTC  
> Updated at: 2019-06-29 07:44:36 UTC  
> Closed at: 2019-06-29 07:44:36 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/78  

Hello,  
  
I am using MSVC 2019 with the default backend (``dbgeng``), and occasionally the function name ends with the null character ``\0``. A simplified example:  
  
```c++  
::boost::stacktrace::stacktrace st(...);  
  
for (auto it = st.crbegin(); it != st.crend(); ++it) {  
  if (it->name().find('\0') != ::std::string::npos) {  
    std::cout << "Null character found!\n";  
  }  
}  
```  
  
The null character seems to be at the end of ``it->name()``, and it seems to happen when the function name is quite long. It is happening, for instance, with a function called  
  
```  
piranha::detail::series_add_term_table<1,1,1,1,0,piranha::series<piranha::polynomials::packed_monomial<int,void>,mppp::rational<1>,void,void>,absl::flat_hash_map<piranha::polynomials::packed_monomial<int,void>,mppp::rational<1>,piranha::detail::series_key_hasher,piranha::detail::series_key_comparer,std::allocator<std::pair<piranha::polynomials::packed_monomial<int,void>const ,mppp::rational<1> > > >,piranha::polynomials::packed_monomial<int,void>,int>  
```  
  
The identical code on OSX with the default backend does not seem to have this issue, so the problem seems to be confined to the ``dbgeng`` backend.
