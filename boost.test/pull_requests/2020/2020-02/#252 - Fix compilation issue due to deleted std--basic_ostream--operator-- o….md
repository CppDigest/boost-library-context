# #252 Fix compilation issue due to deleted std::basic_ostream::operator<< o… [Closed]

> Username: stac47  
> Created at: 2020-02-17 09:01:23 UTC  
> Updated at: 2020-04-21 14:23:39 UTC  
> Closed at: 2020-03-29 16:29:45 UTC  
> Url: https://github.com/boostorg/test/pull/252  

This PR tries to fix the following compilation error:  
```  
In file included from libs/test/src/test_tools.cpp:16:  
./boost/test/impl/test_tools.ipp: In member function ‘void boost::test_tools::tt_detail::print_log_value<const wchar_t*>::operator()(std::ostream&, const wchar_t*)’:                                                                                                                    ./boost/test/impl/test_tools.ipp:127:38: error: use of deleted function ‘std::basic_ostream<char, _Traits>& std::operator<<(std::basic_ostream<char, _Traits>&, const wchar_t*) [with _Traits = std::char_traits<char>]’                                                                   127 |     ostr << ( t ? t : L"null string" );  
      |                                      ^  
In file included from /remote/tools/Linux/2.6/1A/toolchain/x86_64-v20.0.8/include/c++/10.0.1/istream:39,  
                 from /remote/tools/Linux/2.6/1A/toolchain/x86_64-v20.0.8/include/c++/10.0.1/sstream:38,  
                 from ./boost/test/utils/wrap_stringstream.hpp:26,                                                                                                                                                                                                                                        from ./boost/test/unit_test_log.hpp:24,  
                 from ./boost/test/tools/old/impl.hpp:19,  
                 from ./boost/test/test_tools.hpp:46,  
                 from ./boost/test/impl/test_tools.ipp:19,  
                 from libs/test/src/test_tools.cpp:16:                                                                                                                                                                                                                                   /remote/tools/Linux/2.6/1A/toolchain/x86_64-v20.0.8/include/c++/10.0.1/ostream:634:5: note: declared here                                                                                                                                                                                  634 |     operator<<(basic_ostream<char, _Traits>&, const wchar_t*) = delete;  
      |     ^~~~~~~~  
```  
The root cause is that many std::basic_ostream::operator<< overload were marked as deleted: https://en.cppreference.com/w/cpp/io/basic_ostream/operator_ltlt2

---

## Comment 1

> Username: stac47  
> Created_at: 2020-02-17 09:05:18 UTC  
> Url: https://github.com/boostorg/test/pull/252#issuecomment-586887239  

Related to #249

---

## Review 2 [Commented]

> Username: raffienficiaud  
> Created_at: 2020-03-28 20:16:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/test/pull/252#pullrequestreview-383346000  

📁 include/boost/test/impl/test_tools.ipp

```diff
 126 | {
 127 |-     ostr << ( t ? t : L"null string" );
 127 |+     ostr << ( t ? reinterpret_cast<char const*>(t) : "null string" );
```

> Username: raffienficiaud  
> Created_at: 2020-03-28 20:16:52 UTC  
> Url: https://github.com/boostorg/test/pull/252#discussion_r399704892  

changed to `const void *`, as you said it is bogus.


---

## Comment 3

> Username: raffienficiaud  
> Created_at: 2020-03-29 14:54:49 UTC  
> Url: https://github.com/boostorg/test/pull/252#issuecomment-605649141  

In develop

---

## Comment 4

> Username: raffienficiaud  
> Created_at: 2020-03-29 16:29:45 UTC  
> Url: https://github.com/boostorg/test/pull/252#issuecomment-605662855  

Fixed in master, thanks!

---
