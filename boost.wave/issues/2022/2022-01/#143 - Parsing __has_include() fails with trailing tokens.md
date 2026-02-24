# #143 - Parsing __has_include() fails with trailing tokens [Closed]

> Username: r0mai  
> Created at: 2022-01-16 11:49:55 UTC  
> Updated at: 2022-01-18 18:46:16 UTC  
> Closed at: 2022-01-18 18:46:16 UTC  
> Url: https://github.com/boostorg/wave/issues/143  

Lines containing a `__has_include()` operator will fail if any token (even whitespace) follows the `__has_include()` expression.  
  
I believe the error can be traced back to this look-ahead loop, which implicitly expects the last token of the line to be the matching closing parenthesis of the `__has_include` expression:  
  
https://github.com/boostorg/wave/blob/b8cbd86ab907f5dd42bc807a37b6729b84013382/include/boost/wave/util/cpp_macromap.hpp#L1789-L1797  
  
  
  
Repro code:  
  
```cpp  
#include <string>  
#include <iostream>  
#include <boost/wave.hpp>  
#include <boost/wave/cpplexer/cpp_lex_iterator.hpp>  
  
int main() {  
#define CASE_1  
#if defined(CASE_1) // BUG: any token after a __has_include expression will fail parsing  
	std::string input =  
		"#if __has_include(<foo.h>) && 0\n"  
		"#endif\n"  
		;  
#elif defined(CASE_2) // BUG: even with trailing whitespace fails  
	std::string input =  
		"#if __has_include(<foo.h>) \n"  
		"#endif\n"  
		;  
#elif defined(CASE_3) // GOOD: no token after __has_include succeeds  
	std::string input =  
		"#if __has_include(<foo.h>)\n"  
		"#endif\n"  
		;  
#endif  
  
	typedef boost::wave::cpplexer::lex_iterator<  
			boost::wave::cpplexer::lex_token<>>  
		lex_iterator_type;  
	typedef boost::wave::context<  
			std::string::iterator, lex_iterator_type>  
		context_type;  
  
	context_type ctx(input.begin(), input.end(), "input.cpp");  
	ctx.set_language(boost::wave::language_support::support_option_has_include);  
  
	auto first = ctx.begin();  
	auto last = ctx.end();  
  
	try {  
		while (first != last) {  
			std::cout << (*first).get_value();  
			++first;  
		}  
	} catch (boost::wave::preprocess_exception& e) {  
		std::cout << boost::wave::preprocess_exception::error_text(e.get_errorcode()) << std::endl;  
		return 1;  
	}  
	std::cout << "Success" << std::endl;  
	return 0;  
}  
```

---

## Comment 1

> Username: jefftrull  
> Created at: 2022-01-18 06:33:24 UTC  
> Url: https://github.com/boostorg/wave/issues/143#issuecomment-1015111424  

@r0mai Thanks for the excellent bug report. Your analysis was correct! I believe the PR branch resolves your issue. Please let me know if not.

---

## Comment 2

> Username: r0mai  
> Created at: 2022-01-18 12:02:55 UTC  
> Url: https://github.com/boostorg/wave/issues/143#issuecomment-1015345047  

@jefftrull I tried the PR branch, and it works perfectly. Thank you very much for the quick fix!
