# #243 - Invalid lookbehind assertion encountered in the regular expression. [Closed]

> Username: FBergemann  
> Created at: 2025-02-06 07:17:01 UTC  
> Updated at: 2025-02-06 09:40:31 UTC  
> Closed at: 2025-02-06 09:40:30 UTC  
> Url: https://github.com/boostorg/regex/issues/243  

regex compilation fails for this:  
  
```  
#include <iostream>  
#include <cassert>  
#include <string>  
#include <boost/regex.hpp>  
  
int main(int, char**)  
{  
        boost::regex reg("(?<=axx|byy|czzz)");  
        std::string correct("hello, world");  
        std::string incorrect("hiya, world");  
  
        if (boost::regex_match(correct, reg) == true) {  
                std::cout << correct << " matches " << std::endl;  
        } else {  
                std::cout << correct << " does not match" << std::endl;  
        }  
  
        if (boost::regex_match(incorrect, reg) == true) {  
                std::cout << incorrect << " matches " << std::endl;  
        } else {  
                std::cout << incorrect << " does not match" << std::endl;  
        }  
  
        return 0;  
};  
```  
```  
> g++ -I/opt2/linux/include -L/opt2/linux/lib -lboost_regex -o regex regex.cpp  
> ./regex  
terminate called after throwing an instance of 'boost::wrapexcept<boost::regex_error>'  
  what():  Invalid lookbehind assertion encountered in the regular expression.  
Aborted (core dumped)  
```  
  
The problem is, that "czzz" has a different length than "axx", and "byy".  
Change "czzz" to "czz" and it works.  
  
  
Perl had the same problem for this example - and pointed more precisely to the problem:  
  
```  
#!/usr/bin/perl  
  
if ("hello, world!" =~ /(?<=axx|byy|czzz)/) {  
        print "matches\n";  
} else {  
        print "does not match\n";  
}  
  
```  
  
**Perl v5.16.3**  
```  
> ./test.pl  
Variable length lookbehind not implemented in regex m/(?<=axx|byy|czzz)/ at ./test.pl line 3.  
```  
  
**Perl v5.32.1**  
```  
> ./test.pl  
Variable length lookbehind is experimental in regex; marked by <-- HERE in m/(?<=axx|byy|czzz) <-- HERE / at ./test.pl line 3.  
does not match  
```  
  
**Perl v5.38.2**  
```  
> ./test.pl  
does not match  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2025-02-06 09:40:30 UTC  
> Url: https://github.com/boostorg/regex/issues/243#issuecomment-2639300559  

We don't support that, and it's documented as such.  Variable length lookbehind is pretty much an abomination to implement IMO.
