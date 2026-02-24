# #123 - Non-word-boundary matches at start and end of string [Closed]

> Username: JubilantJerry  
> Created at: 2021-04-07 21:13:34 UTC  
> Updated at: 2021-10-08 10:05:56 UTC  
> Closed at: 2021-10-08 10:05:56 UTC  
> Url: https://github.com/boostorg/regex/issues/123  

According to the documentation, "\B" matches locations that are not at word boundaries. The behavior is inconsistent with the description at the start and end of strings, however.  
  
Example code:  
```  
#include <iostream>  
#include <boost/regex.hpp>  
  
int main() {  
    const char* subject_non_word = "!";  
    const char* pattern_non_word = "\\B";  
    boost::regex regex_non_word = boost::regex(pattern_non_word);  
    std::cout << boost::regex_search(subject_non_word, regex_non_word) << std::endl;  
  
    const char* subject_word = "a";  
    const char* pattern_word = "\\b";  
    boost::regex regex_word = boost::regex(pattern_word);  
    std::cout << boost::regex_search(subject_word, regex_word) << std::endl;  
}  
```  
  
The output, tested on Boost 1.75.0:  
```  
0  
1  
```  
  
If we interpret the wording as saying "\B" represents all locations that are not matched by "\b", then this is not the correct behavior since "\b" does not match the start and end of the string when the subject is "!".  
  
If we only consider locations between two characters as eligible for being or not being word boundaries, then it's inconsistent that the start and end of the string are considered word boundaries when the subject is "a".  
  
If we assume there are invisible characters before and after the string, then those invisible characters must neither be word characters nor non-word characters. If they are word characters, then there are no word boundaries when the subject is "a". If they are non-word characters, then when the subject is "!" the start and end of the string represent locations between two non-word characters and should match "\B".

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-10-06 15:00:50 UTC  
> Url: https://github.com/boostorg/regex/issues/123#issuecomment-936449323  

The intention was that \B matches a non-word boundary *within* a word.  However, I see that perl interprets it more as you do, so I'll fix that shortly.

---

## Comment 2

> Username: JohnAlt2  
> Created at: 2021-10-06 15:30:16 UTC  
> Url: https://github.com/boostorg/regex/issues/123#issuecomment-936504109  

I agree with changing this to follow the general convention of \<Uppercase> being inverse of \<lowercase>, and more importantly if it conforms with Perl regex behaviour. But suggest the the change release notes clearly and strongly makes clear the change in behaviour as pre-existing Boost regular expressions may be relying on previous behaviour. After all I always preach that boundary test are on of the two most important regex habits to develop 8¬)
