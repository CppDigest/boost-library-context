# #137 - Simple unknown directive => found_unknown_directive is not called, stripped of pound. [Closed]

> Username: realazthat  
> Created at: 2021-12-17 02:36:29 UTC  
> Updated at: 2022-02-21 16:34:46 UTC  
> Closed at: 2022-01-02 22:24:39 UTC  
> Url: https://github.com/boostorg/wave/issues/137  

* I am using boost wave, to preprocess glsl.   
* glsl uses a directive at the top, called `#version`.  
* Unfortunately the resulting output for some reason just strips the `#` symbol.  
* Furthermore, the hook I have to print all unknown directives is not called.  
* As a workaround, I have found that using a macro replacement works.  
  
My minimal reproducible example and output:   
  
```c++  
// g++-11 boost-wave-reproduction.cxx  --std=c++17 -lboost_wave -lboost_thread -lboost_filesystem  
// ./a.out  
  
#include <string>  
#include <filesystem>  
#include <iostream>  
  
#include "boost/wave.hpp"  
#include "boost/wave/cpp_context.hpp"  
#include "boost/wave/cpp_exceptions.hpp"  
#include "boost/wave/cpplexer/cpp_lex_iterator.hpp"  
#include "boost/wave/language_support.hpp"  
  
  
template<typename Token>  
struct ContextPolicy : boost::wave::context_policies::eat_whitespace<Token> {  
  typedef boost::wave::context_policies::eat_whitespace<Token> base_type;  
  
  
  template <typename ContextT, typename TokenT>  
  bool found_directive(ContextT const &ctx,   
      TokenT const &directive) {  
    std::cout << "found_directive, directive.get_value(): " << directive.get_value() << std::endl;  
    return this->base_type::found_directive(ctx, directive);  
  }  
  
  template <typename ContextT, typename ContainerT>  
  bool found_unknown_directive(ContextT const& ctx,  
      ContainerT const& line, ContainerT& pending) {  
    std::cout << "found_unknown_directive, line: ";  
    for (const auto& token : line) {  
      std::cout << token.get_value() << ",";  
    }  
    std::cout << std::endl;  
    return this->base_type::found_unknown_directive(ctx, line, pending);  
  }  
  
  template <typename ContextT, typename TokenT>  
  TokenT const& generated_token(ContextT const &ctx,   
      TokenT const& token) {  
    std::cout << "generated_token()" << std::endl;  
    std::cout << " token.is_valid(): " << (token.is_valid() ? "true" : "false") << std::endl;  
    boost::wave::token_id token_id = boost::wave::token_id(token);  
    std::cout << " token_id: " << token_id << std::endl;  
  
    if (token.is_valid()) {  
      std::cout << " boost::wave::get_token_name(token_id): " << boost::wave::get_token_name(token_id) << std::endl;  
      std::cout << " token.get_value(): " << token.get_value() << std::endl;  
    }  
    return this->base_type::generated_token(ctx, token);  
  }  
};  
  
  
int main() {  
  
  typedef boost::wave::cpplexer::lex_iterator<  
      boost::wave::cpplexer::lex_token<>>  
      lex_iterator_type;  
  typedef boost::wave::context<  
    /*Iterator=*/std::string::const_iterator,  
    /*LexIterator=*/lex_iterator_type,  
    /*InputPolicy=*/boost::wave::iteration_context_policies::load_file_to_string,  
    /*ContextPolicy=*/ContextPolicy<typename lex_iterator_type::token_type>>  
      context_type;  
  
  std::filesystem::path source_path = "/path/to/source.cxx";  
  std::string source = R"source(  
// This gets replaced by "#version" as expected.  
TEST  
// The pound sign doesn't appear, and found_unknown_directive is not called.  
#version  
)source";  
  
  context_type ctx(/*first_=*/source.begin(),  
                      /*last_=*/source.end(),  
                      /*fname=*/source_path.string().c_str());  
  ctx.set_language(/*language=*/boost::wave::language_support::support_cpp11);  
  
  assert(ctx.add_macro_definition(  
      /*macrostring=*/"TEST=#version"));  
  context_type::iterator_type first = ctx.begin();  
  context_type::iterator_type last = ctx.end();  
  
  
  std::ostringstream sstr;  
  while (first != last) {  
    sstr << (*first).get_value();  
    ++first;  
  }  
  
  std::string preprocessed_source = sstr.str();  
  
  std::cout << preprocessed_source << std::endl;  
  return 0;  
}  
  
```  
  
Output:  
  
```  
$ ./a.out   
generated_token()  
 token.is_valid(): true  
 token_id: 402915724  
 boost::wave::get_token_name(token_id): POUND  
 token.get_value(): #  
generated_token()  
 token.is_valid(): true  
 token_id: 134480252  
 boost::wave::get_token_name(token_id): IDENTIFIER  
 token.get_value(): version  
generated_token()  
 token.is_valid(): true  
 token_id: 1476395402  
 boost::wave::get_token_name(token_id): NEWLINE  
 token.get_value():   
  
generated_token()  
 token.is_valid(): true  
 token_id: 134480252  
 boost::wave::get_token_name(token_id): IDENTIFIER  
 token.get_value(): version  
generated_token()  
 token.is_valid(): true  
 token_id: 1476395402  
 boost::wave::get_token_name(token_id): NEWLINE  
 token.get_value():   
  
generated_token()  
 token.is_valid(): true  
 token_id: 1610613137  
 boost::wave::get_token_name(token_id): EOF  
 token.get_value():   
generated_token()  
 token.is_valid(): false  
 token_id: 1610613138  
#version  
version  
  
```  
  
Expected output:  
  
```  
...  
found_unknown_directive ...  
...  
#version  
#version  
```

---

## Comment 1

> Username: jefftrull  
> Created at: 2021-12-18 08:13:55 UTC  
> Url: https://github.com/boostorg/wave/issues/137#issuecomment-997167988  

I don't know what the problem is yet, but I _do_ have a workaround for you to try:  
  
Replace this line:  
```c++  
ctx.set_language(/*language=*/boost::wave::language_support::support_cpp11);  
```  
  
with these two:  
  
```c++  
ctx.set_language(boost::wave::enable_long_long(ctx.get_language()));  
ctx.set_language(boost::wave::enable_variadics(ctx.get_language()));  
```  
  
C++11 support consists of three things: `long long`, variadics, and a removal of a warning when the input does not end in a newline. It seems like the third feature is interacting with the `found_unknown_directive` hook.

---

## Comment 2

> Username: jefftrull  
> Created at: 2021-12-19 01:56:57 UTC  
> Url: https://github.com/boostorg/wave/issues/137#issuecomment-997315252  

I think I have a fix, and a tentative root cause... the "ignore lack of newline before EOF" feature is checked in one place unconditionally, regardless of whether we are actually looking at EOF. Can you try changing [this line](https://github.com/boostorg/wave/blob/07f853eda7352f9b974a47c08bda6337174b995f/include/boost/wave/util/cpp_iterator.hpp#L967) to:  
  
```c++  
    return need_no_newline_at_end_of_file(ctx.get_language()) &&  
        ((it == end) || (T_EOF == token_id(*it)));  
```  
and let me know if it works for you?

---

## Comment 3

> Username: realazthat  
> Created at: 2021-12-19 11:07:55 UTC  
> Url: https://github.com/boostorg/wave/issues/137#issuecomment-997372417  

> and let me know if it works for you?  
  
Nope, I don't think it is working :(

---

## Comment 4

> Username: jefftrull  
> Created at: 2021-12-19 18:16:19 UTC  
> Updated at: 2021-12-19 18:32:41 UTC  
> Url: https://github.com/boostorg/wave/issues/137#issuecomment-997436954  

OK! did the behavior change in any way? For me, this causes the `found_unknown_directive` callback to start firing. New output:  
  
```  
generated_token()  
 token.is_valid(): true  
 token_id: 402915724  
 boost::wave::get_token_name(token_id): POUND  
 token.get_value(): #  
generated_token()  
 token.is_valid(): true  
 token_id: 134480252  
 boost::wave::get_token_name(token_id): IDENTIFIER  
 token.get_value(): version  
generated_token()  
 token.is_valid(): true  
 token_id: 1476395402  
 boost::wave::get_token_name(token_id): NEWLINE  
 token.get_value():   
  
found_unknown_directive, line: #,version,  
,  
terminate called after throwing an instance of 'boost::wrapexcept<boost::wave::preprocess_exception>'  
  what():  boost::wave::preprocess_exception  
Aborted  
```

---

## Comment 5

> Username: realazthat  
> Created at: 2021-12-20 00:01:23 UTC  
> Updated at: 2021-12-20 00:01:43 UTC  
> Url: https://github.com/boostorg/wave/issues/137#issuecomment-997487676  

OK my bad, it was still using the system boost, which I uninstalled and now it worked.  
```bash  
$ LD_LIBRARY_PATH=$HOME/boost/stage/lib/ ./a.out  
found_unknown_directive, line: #,version,  
,  
terminate called after throwing an instance of 'boost::wrapexcept<boost::wave::preprocess_exception>'  
  what():  boost::wave::preprocess_exception  
Aborted  
```  
  
Note, I commented out the generated_token() logging, so this is consistent with your output.

---

## Comment 6

> Username: jefftrull  
> Created at: 2021-12-20 00:03:40 UTC  
> Url: https://github.com/boostorg/wave/issues/137#issuecomment-997488204  

Great! OK, what else is left? Any other behavior that seems wrong?

---

## Comment 7

> Username: realazthat  
> Created at: 2021-12-20 22:27:01 UTC  
> Url: https://github.com/boostorg/wave/issues/137#issuecomment-998317525  

> Great! OK, what else is left? Any other behavior that seems wrong?  
  
I think that does it.
