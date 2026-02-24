# #111 - Vertical tab is erroneously included in the blank character class [Closed]

> Username: Aster89  
> Created at: 2020-09-22 12:32:45 UTC  
> Updated at: 2022-10-02 16:00:29 UTC  
> Closed at: 2022-10-02 16:00:29 UTC  
> Url: https://github.com/boostorg/regex/issues/111  

Per POSIX, `[[:blank:]]` should only contain space and (horizontal) tab, the vertical tab being in `[[:space:]]`. However, the following code shows that boost puts the vertical space in `[[:blank:]]` too.  
  
```  
#include <regex>  
#include <assert.h>  
#include <boost/regex.hpp>  
#include <iostream>  
#include <boost/version.hpp>  
int main() {  
    std::cout << BOOST_VERSION % 100 << ' ' << BOOST_VERSION / 100 % 1000 << ' ' << BOOST_VERSION / 100000 << '\n';  
    const auto VT = "\x0B";  
    assert( std::regex_match(VT  , std::regex{"^[[:space:]]+$"}));  
    assert(!std::regex_match(VT  , std::regex{"^[[:blank:]]+$"}));  
    assert( boost::regex_match(VT  , boost::regex{"^[[:space:]]+$"}));  
    assert(!boost::regex_match(VT  , boost::regex{"^[[:blank:]]+$"})); // fails  
}  
```

---

## Comment 1

> Username: JohnAlt2  
> Created at: 2022-10-02 14:58:45 UTC  
> Updated at: 2022-10-02 15:03:26 UTC  
> Url: https://github.com/boostorg/regex/issues/111#issuecomment-1264663069  

Testing with what is probably 1.64.0 release does not show [:blank:] detecting vertical tabs in practise.   
See image:  
![image](https://user-images.githubusercontent.com/46526650/193460141-f2a9b92c-9457-4f4d-9645-1f3cbea71077.png)  
Interesting to see that:   
  
- "\v" detects Vertical Tab, Line Feed & Carriage Return - Not just Vertical Tab.  
- "[:blank:] detects extended ASCII space characters but NOT Unicode space characters above 8 bits.  
  
What file was this seen in?  
  
Marking this ticket for closure in a month if there is no further info.

---

## Comment 2

> Username: Aster89  
> Created at: 2022-10-02 16:00:29 UTC  
> Url: https://github.com/boostorg/regex/issues/111#issuecomment-1264675955  

I can't replicate it anymore. Should have created a live example with compiler explorer at that time. Sorry.
