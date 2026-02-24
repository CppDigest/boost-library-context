# #183 - Removal of line continuations doesn't insert line directive. [Closed]

> Username: realazthat  
> Created at: 2022-12-12 21:34:01 UTC  
> Updated at: 2023-10-23 00:21:19 UTC  
> Closed at: 2023-10-23 00:21:18 UTC  
> Url: https://github.com/boostorg/wave/issues/183  

When running wave on the following code:  
  
```c++  
/*  
 Thing that goes here \  
 Continuation inside a comment here \  
 I dunno what is supposed to happen to the continuations, or if it is disallowed, but that's OKAY.  
*/  
  
int main() {  
  return 0;  
}  
```  
It gets preprocessed into the following:  
  
```c++  
/*  
 Thing that goes here  Continuation inside a comment here  I dunno what is supposed to happen to the continuations, or if it is disallowed, but that's OKAY.  
*/  
  
int main() {  
  return 0;  
}  
```  
  
Note that the line continuations were removed, but no `#line` directive was emitted to fix the line numbers.  
  
Note, `ctx.set_language(/*language=*/enable_preserve_comments(ctx.get_language()));` option was set, to preserve the comments.  
  
Note, I have not tested if this problem occurs outside a comment, but a comment makes it more complicated, since the line directive would presumably need to be placed outside of the comment.  
  
## Minimum reproducible example:  
   
```c++  
  
#include <iostream>  
#include <sstream>  
#include <string>  
  
#include "boost/wave.hpp"  
#include "boost/wave/cpp_context.hpp"  
#include "boost/wave/cpp_exceptions.hpp"  
#include "boost/wave/cpplexer/cpp_lex_iterator.hpp"  
#include "boost/wave/language_support.hpp"  
  
  
int main() {  
  using namespace boost::wave::context_policies;  
  using namespace boost::wave;  
  using ContextPolicy_ = default_preprocessing_hooks;  
  using lex_iterator_type = cpplexer::lex_iterator<cpplexer::lex_token<>>;  
  using context_type =  
      context<std::string::const_iterator, lex_iterator_type,  
              iteration_context_policies::load_file_to_string, ContextPolicy_>;  
  
  const std::string shader_source = R"str(  
/*  
 Thing that goes here \  
 Continuation inside a comment here \  
 I dunno what is supposed to happen to the continuations, or if it is disallowed, but that's OKAY.  
*/  
  
int main() {  
  return 0;  
}  
)str";  
  std::string shader_path = "code.cc";  
  
  context_type ctx(/*first_=*/shader_source.begin(),  
                   /*last_=*/shader_source.end(),  
                   /*fname=*/shader_path.c_str());  
  ctx.set_language(  
      /*language=*/enable_preserve_comments(ctx.get_language()));  
  ctx.set_language(  
      /*language=*/enable_emit_line_directives(ctx.get_language()));  
  
  context_type::iterator_type first = ctx.begin();  
  context_type::iterator_type last = ctx.end();  
  
  
  std::ostringstream sstr;  
  while (first != last) {  
    sstr << (*first).get_value();  
    ++first;  
  }  
  
  std::string preprocessed = sstr.str();  
  std::cout << preprocessed;  
  return 0;  
}  
  
```

---

## Comment 1

> Username: jefftrull  
> Created at: 2022-12-12 21:49:14 UTC  
> Url: https://github.com/boostorg/wave/issues/183#issuecomment-1347389311  

FWIW the wave tool gets the right output here with `-p 2`

---

## Comment 2

> Username: jefftrull  
> Created at: 2022-12-12 23:21:25 UTC  
> Url: https://github.com/boostorg/wave/issues/183#issuecomment-1347483301  

It looks like using `eat_whitespace` instead of `default_preprocessor_hooks` fixes the issue. I can no longer remember why these are different things or why one would produce line directives and the other not.

---

## Comment 3

> Username: jefftrull  
> Created at: 2023-04-17 05:05:26 UTC  
> Url: https://github.com/boostorg/wave/issues/183#issuecomment-1510706666  

I think this is arguably "working as designed" so if you don't have a specific complaint I think we should close this.

---

## Comment 4

> Username: jefftrull  
> Created at: 2023-10-23 00:21:19 UTC  
> Url: https://github.com/boostorg/wave/issues/183#issuecomment-1774246538  

Closing per above message, please reopen if you think we should pursue further.
