# #188 - Segmentation fault when "#pragma\n" is encountered [Closed]

> Username: mfep  
> Created at: 2023-07-13 09:52:05 UTC  
> Updated at: 2023-10-26 07:35:57 UTC  
> Closed at: 2023-10-26 00:28:17 UTC  
> Url: https://github.com/boostorg/wave/issues/188  

### System info  
- Boost version: 1.82.0  
- Compiler: g++ 13.0.1-x86_64-redhat-linux  
- Both in optimized and unoptimized build  
  
### Actual behaviour  
Preprocessing C++ source code with a `#pragma` statement immediately followed by a newline (`\n`) using Wave crashes the program with a segmentation fault.  
  
### Expected behaviour  
The preprocessor lets the `#pragma` statement through to the output as unrecognized. Or throw a recoverable error.  
  
### Minimal reproducer  
Minimal reproducer based on the "Quick start" section in the documentation:  
  
```c++  
#include <boost/wave/cpplexer/cpp_lex_iterator.hpp>  
#include <boost/wave.hpp>  
  
#include <iostream>  
#include <string>  
  
int main()  
{  
    std::string input =  
        R"(int main()  
{  
#pragma  
    return 0;  
}  
)";  
  
    typedef boost::wave::cpplexer::lex_iterator<  
        boost::wave::cpplexer::lex_token<>>  
        lex_iterator_type;  
    typedef boost::wave::context<  
        std::string::iterator, lex_iterator_type>  
        context_type;  
  
    context_type ctx(input.begin(), input.end());  
  
    context_type::iterator_type first = ctx.begin();  
    context_type::iterator_type last = ctx.end();  
  
    try  
    {  
        while (first != last)  
        {  
            std::cout << (*first).get_value();  
            ++first;  
        }  
    }  
    catch (const boost::wave::preprocess_exception &ex)  
    {  
        std::cout << "ERROR: " << ex.description() << std::endl;  
        return -1;  
    }  
}  
```

---

## Comment 1

> Username: hkaiser  
> Created at: 2023-07-14 13:25:51 UTC  
> Url: https://github.com/boostorg/wave/issues/188#issuecomment-1635864383  

Thanks for reporting, I'll try to have a look.

---

## Comment 2

> Username: jefftrull  
> Created at: 2023-07-15 04:08:14 UTC  
> Url: https://github.com/boostorg/wave/issues/188#issuecomment-1636650826  

Thanks for the very clear bug report.  
  
It looks like the code that implements pragmas assumes there is always at least one token (the space) after `#pragma` and when it is not present we can dereference invalid memory. You can see this [here](https://github.com/boostorg/wave/blob/61abf8b6b98d8937515a4eddf6d0d7274921dcda/include/boost/wave/util/cpp_iterator.hpp#L2432) where we unconditionally increment the iterator representing the start of the token sequence and then dereference it.  
  
It looks like there's a simple fix: testing for an empty token sequence and returning early. It may not be the best fix, but it should be a usable workaround. Add this at the start of `on_pragma`:  
  
```  
    if (begin == end)  
        return true;  
```

---

## Comment 3

> Username: hkaiser  
> Created at: 2023-07-15 11:32:18 UTC  
> Updated at: 2023-07-15 11:33:53 UTC  
> Url: https://github.com/boostorg/wave/issues/188#issuecomment-1636742121  

I think another possible fix would be to change this:  
https://github.com/boostorg/wave/blob/61abf8b6b98d8937515a4eddf6d0d7274921dcda/include/boost/wave/grammars/cpp_grammar.hpp#L541-L550  
to use `+` instead of `*`. This change would cause it to report an illformed preprocessor directive for the code above.  
  
Also, I should go back to read the standard to see what it has to say about `#pragma` preprocessor directives.

---

## Comment 4

> Username: hkaiser  
> Created at: 2023-07-15 11:36:41 UTC  
> Url: https://github.com/boostorg/wave/issues/188#issuecomment-1636742826  

> Also, I should go back to read the standard to see what it has to say about `#pragma` preprocessor directives.  
  
Well, here we go: https://eel.is/c++draft/cpp.pragma. My suggested fix is incorrect, then. A line with just a `#pragma` directive is perfectly fine. @jefftrull you suggested the correct fix above. Thanks!

---

## Comment 5

> Username: jefftrull  
> Created at: 2023-10-26 00:28:17 UTC  
> Url: https://github.com/boostorg/wave/issues/188#issuecomment-1780236179  

A fix for this has been merged into master and will be released as part of Boost 1.84

---

## Comment 6

> Username: mfep  
> Created at: 2023-10-26 07:35:57 UTC  
> Url: https://github.com/boostorg/wave/issues/188#issuecomment-1780571948  

Thank you, @jefftrull!
