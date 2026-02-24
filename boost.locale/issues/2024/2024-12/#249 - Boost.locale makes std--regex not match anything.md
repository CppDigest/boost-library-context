# #249 - Boost.locale makes std::regex not match anything [Open]

> Username: Lord-Kamina  
> Created at: 2024-12-19 19:15:29 UTC  
> Updated at: 2025-01-17 10:12:13 UTC  
> Url: https://github.com/boostorg/locale/issues/249  

I had initially posted a comment in #35, but maybe it deserves its own issue instead.  
I think it's essentially the same problem, except I'm on macOS 13,  
```bash  
$ clang++ -v  
Apple clang version 15.0.0 (clang-1500.1.0.2.5)  
Target: x86_64-apple-darwin22.6.0.  
```  
  
I'm using boost 1.86.0, built against ICU 74.2  
  
I had seen this behavior before, and have never found a real solution. I now stumbled upon it again on a project. I spent about two days trying to tune my regex, thinking I must have made a mistake. Eventually I began simplifying it and simplifying it, without it resolving.  
  
Eventually, I decided to make a minimal example to test it; so I have following code:  
  
```c++  
#include <boost/locale.hpp>  
#include <iostream>  
#include <locale>  
#include <regex>  
#include <string>  
  
int main() {  
	boost::locale::generator locGen;  
	const std::locale loc = locGen("en_US.UTF-8");  
// 	std::locale::global(loc);    
	auto pattern = std::regex(R"(^(?:\s)*([_[:alnum:].-]+)\s*=\s*([^;#\n\r]+)*)");  
// 	pattern.imbue(loc);  
	const std::string text{"  pozo = mani"};  
	std::smatch result;  
	std::regex_search(text, result, pattern);  
	std::cout << "ready: " << result.ready() << ", size: " << result.size() << std::endl;  
	for (size_t i=0; i < result.size(); i++) {  
		std::cout << "match[" <<i<<"]: " << result[i] <<std::endl;  
	}  
	return 0;  
}  
```  
  
Which outputs  
  
```bash  
$ clang++ -o regex_test regex_test.cpp -std=c++17 -I/opt/local/include/ -lboost_locale-mt -lboost_system-mt -L/opt/local/lib && ./regex_test  
ready: 1, size: 3  
match[0]:   pozo = mani  
match[1]: pozo  
match[2]: mani  
```  
  
If I uncomment the `std::locale::global` line (with or without the `pattern.imbue`), this happens instead:  
  
```bash  
clang++ -o regex_test regex_test.cpp -std=c++17 -I/opt/local/include/ -lboost_locale-mt -lboost_system-mt -L/opt/local/lib && ./regex_test  
ready: 1, size: 0  
```  
  
I tried changing facets gradually, OR'ing them one by one and it always worked until I added `std::locale::collate`. From that point, removing all the others and keeping just `std::locale::locate`, still makes the regex not work.  
  
```c++  
#include <boost/locale.hpp>  
#include <iostream>  
#include <locale>  
#include <regex>  
#include <string>  
  
int main() {  
	boost::locale::generator locGen;  
	const std::locale loc = locGen("en_US.UTF-8");  
	std::locale testLoc = std::locale(std::locale::classic(), loc, std::locale::collate);  
	std::locale::global(testLoc);  
	auto pattern = std::regex(R"(^(?:\s)*([_[:alnum:].-]+)\s*=\s*([^;#\n\r]+)*)");  
// 	pattern.imbue();  
	const std::string text{"  pozo = mani"};  
	std::smatch result;  
	std::regex_search(text, result, pattern);  
	std::cout << "ready: " << result.ready() << ", size: " << result.size() << std::endl;  
	for (size_t i=0; i < result.size(); i++) {  
		std::cout << "match[" <<i<<"]: " << result[i] <<std::endl;  
	}  
	return 0;  
}  
```  
  
That already doesn't work.

---

## Comment 1

> Username: Lord-Kamina  
> Created at: 2024-12-19 21:18:44 UTC  
> Url: https://github.com/boostorg/locale/issues/249#issuecomment-2555791993  

Of note, this does not seem to happen with gcc and libstdc++. I have not yet tried mixing clang with libstc++ nor gcc with libc++.

---

## Comment 2

> Username: Flamefire  
> Created at: 2025-01-12 18:09:10 UTC  
> Url: https://github.com/boostorg/locale/issues/249#issuecomment-2585854607  

in #35 it is also reported to fail with libc++. Also  
> C and POSIX work always ok. Every locale is affected: Even en_US.UTF-8.  
  
Seemingly `collation_facet` is the culprit which was reported there and your example suggests the same.

---

## Comment 3

> Username: Flamefire  
> Created at: 2025-01-13 12:49:44 UTC  
> Updated at: 2025-01-17 10:11:32 UTC  
> Url: https://github.com/boostorg/locale/issues/249#issuecomment-2587020563  

I was able to reproduce this on Linux with Clang 14.0.0 and libc++ but not with Clang 15.0.7  
  
The minimal reproducer seems to be matching `R"(\s=)"` against `" ="`. Using a space instead of `\s` or a letter or number instead of the equals sign succeeds.  
  
Edit: There seems to be a bug in libc++:  
  
```  
#include <boost/locale.hpp>  
#include <iostream>  
#include <locale>  
#include <regex>  
#include <string>  
  
int main() {  
	boost::locale::generator locGen;  
	const std::locale loc = locGen("en_US.UTF-8");  
    //std::locale::global(loc); // Uncomment this and it will fail  
    std::regex pattern;  
 	pattern.imbue(loc);  
    pattern = R"(\s=)";  
	const std::string text{" ="};  
	std::smatch result;  
	std::regex_search(text, result, pattern);  
	std::cout << "ready: " << result.ready() << ", size: " << result.size() << std::endl;  
	for (size_t i=0; i < result.size(); i++) {  
		std::cout << "match[" <<i<<"]: " << result[i] <<std::endl;  
	}  
	return !result.size();  
}  
```  
  
The issue doesn't happen when using `imbue` but only when changing the global locale.

---

## Comment 4

> Username: Flamefire  
> Created at: 2025-01-17 10:11:24 UTC  
> Updated at: 2025-01-17 10:12:13 UTC  
> Url: https://github.com/boostorg/locale/issues/249#issuecomment-2597886961  

I commented in https://github.com/llvm/llvm-project/issues/39399 as I suspect this is a bug in libc++ which doesn't expect the behavior of the collation facet of Boost.Locale. Let's see what they say about that.  
  
There certainly is a bug in libc++ as `imbue` does not have an effect on the matching, only the global locale matters.
