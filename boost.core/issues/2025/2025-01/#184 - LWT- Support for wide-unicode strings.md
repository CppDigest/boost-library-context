# #184 - LWT: Support for wide/unicode strings [Open]

> Username: Flamefire  
> Created at: 2025-01-03 09:35:58 UTC  
> Updated at: 2025-01-03 09:35:58 UTC  
> Url: https://github.com/boostorg/core/issues/184  

Currently `BOOST_TEST_EQ(std::u16string{}, std::u16string{})` fails to compile. Similar for `wstring`, `u32string` and `u8string`.  
  
Additionally support for `char8_t` comparison is missing.  
  
While implementing this I'd suggest to try printing the ASCII subset of those directly similar to the `char` handling. The current differences in output are a bit confusing:  
```  
: test 's[0] == s2[1]' ('1' == '2') failed in function 'void test(const Ch *) [Ch = char]'  
: test 's[0] == s2[1]' ('49' == '50') failed in function 'void test(const Ch *) [Ch = wchar_t]'  
: test 's[0] == s2[1]' ('49' == '50') failed in function 'void test(const Ch *) [Ch = char16_t]'  
: test 's[0] == s2[1]' ('49' == '50') failed in function 'void test(const Ch *) [Ch = char32_t]'  
```
