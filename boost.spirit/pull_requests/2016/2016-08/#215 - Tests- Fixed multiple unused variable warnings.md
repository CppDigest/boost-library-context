# #215 Tests: Fixed multiple unused variable warnings [Merged]

> Username: Kojoley  
> Created at: 2016-08-25 19:33:55 UTC  
> Updated at: 2017-11-12 18:58:39 UTC  
> Merged at: 2016-08-25 22:23:44 UTC  
> Closed at: 2016-08-25 22:23:44 UTC  
> Url: https://github.com/boostorg/spirit/pull/215  

```  
lex/token_iterpair.cpp:111:49: warning: unused variable 'value' [-Wunused-variable]  
        typename Token::token_value_type const& value (t.value());  
                                                ^  
lex/token_iterpair.cpp:129:49: warning: unused variable 'value' [-Wunused-variable]  
        typename Token::token_value_type const& value (t.value());  
                                                ^  
karma/pattern4.cpp: In function ‘int main()’:  
karma/pattern4.cpp:82:44: warning: typedef ‘var_type’ locally defined but not used [-Wunused-local-typedefs]  
         typedef variant<char, int, double> var_type;  
                                            ^  
karma/pattern4.cpp:100:44: warning: typedef ‘var_type’ locally defined but not used [-Wunused-local-typedefs]  
         typedef variant<char, int, double> var_type;  
                                            ^  
lex/dedent_handling_phoenix.cpp: In function ‘int main()’:  
lex/dedent_handling_phoenix.cpp:86:39: warning: typedef ‘iterator’ locally defined but not used [-Wunused-local-typedefs]  
     typedef lexer_type::iterator_type iterator;  
                                       ^  
```

---

## Comment 1

> Username: Kojoley  
> Created_at: 2016-08-25 22:14:31 UTC  
> Url: https://github.com/boostorg/spirit/pull/215#issuecomment-242560212  

@djowel can we have this merged, or it is something bad with the PR?

---

## Comment 2

> Username: djowel  
> Created_at: 2016-08-25 22:23:37 UTC  
> Url: https://github.com/boostorg/spirit/pull/215#issuecomment-242562755  

Oh, thanks for reminding me! :)

---
