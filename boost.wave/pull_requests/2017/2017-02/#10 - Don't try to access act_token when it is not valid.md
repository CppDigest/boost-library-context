# #10 Don't try to access act_token when it is not valid [Merged]

> Username: hia3  
> Created at: 2017-02-18 01:53:19 UTC  
> Updated at: 2017-02-18 02:17:59 UTC  
> Merged at: 2017-02-18 02:17:59 UTC  
> Closed at: 2017-02-18 02:17:59 UTC  
> Url: https://github.com/boostorg/wave/pull/10  

`lex_token::get_value` assumes that `lex_token::data` is not NULL. When it  
is NULL it causes UB detectable by clang's AddressSanitizer:  
  
cpp_lex_token.hpp:242:57: runtime error: member call on null pointer of  
type `boost::wave::cpplexer::impl::token_data...`  
  
  
Another way to fix this - return default-constructed lex_token::string_type, but lex_token::get_value returns it by reference, so it has to be stored somewhere.

---
