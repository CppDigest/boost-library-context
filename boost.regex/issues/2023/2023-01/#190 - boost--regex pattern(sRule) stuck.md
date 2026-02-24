# #190 - boost::regex pattern(sRule) stuck [Closed]

> Username: lq2008  
> Created at: 2023-01-09 07:56:28 UTC  
> Updated at: 2023-02-16 12:36:36 UTC  
> Closed at: 2023-02-16 12:36:35 UTC  
> Url: https://github.com/boostorg/regex/issues/190  

when Rule is ((([A-Za-z]{3,9}:(?:\/\/)?)(?:[-;:&=\+\$,\w]+@)?[A-Za-z0-9.-]+(:[0-9]+)?|(?:ww‌​w.|[-;:&=\+\$,\w]+@)[A-Za-z0-9.-]+)((?:\/[\+~%\/.\w-_]*)?\??(?:[-\+=&;%@.\w_]*)#?‌​(?:[\w]*))?), execute  boost::regex pattern(sRule) ，program stuck,  I use pcre lib，not exist problem;

---

## Comment 1

> Username: JohnAlt2  
> Created at: 2023-01-29 11:42:03 UTC  
> Url: https://github.com/boostorg/regex/issues/190#issuecomment-1407639680  

Hi,  
A) Can you provide a sample text of what this regex should match on.  
B) Can you more precisely explain what you mean by "program stuck" ... "not exist problem"?  
C) Is your programming able to capture any exception calls from the call attempt?

---

## Comment 2

> Username: JohnAlt2  
> Created at: 2023-01-29 14:21:44 UTC  
> Url: https://github.com/boostorg/regex/issues/190#issuecomment-1407677046  

The expression itself is broken here "(?:/[+~%/.\w-]*)???" with the triple unescaped question marks.  
Is your issue caused by your expression is not working?  
Or is it about how Boost:Regex responds to this invalid expression?

---

## Comment 3

> Username: lq2008  
> Created at: 2023-02-16 03:17:58 UTC  
> Url: https://github.com/boostorg/regex/issues/190#issuecomment-1432437600  

sample:http://www.123.com:8086/1.html

---

## Comment 4

> Username: lq2008  
> Created at: 2023-02-16 09:44:29 UTC  
> Url: https://github.com/boostorg/regex/issues/190#issuecomment-1432801314  

program stop at boost::regex pattern(sRule) , use pcrelib function pcre_compile compile rule, no problem.

---

## Comment 5

> Username: JohnAlt2  
> Created at: 2023-02-16 10:50:13 UTC  
> Updated at: 2023-02-16 10:50:42 UTC  
> Url: https://github.com/boostorg/regex/issues/190#issuecomment-1432890998  

Failed to run until reducing the "???" sequence down to "??" on the following on the following testers:  
  
- https://code.google.com/archive/p/bregextest/ (uses Boost)  
- https://regex101.com/ (for all 8 flavours of regex on the site)  
  
(in some cases the "/" and "-" characters have to escaped in order to test)  
  
There is no "???" operator or sequence in the PCRE.org  listed syntax:  
https://www.pcre.org/original/doc/html/pcresyntax.html  
  
If this runs and detects in PCRE Lib then I suspect that is a bug in that library?  
If you are certain that "???" is a valid syntax, can you provide a link to a documentation page that lists this operator in the other library or Boost?  
  
Even if a specific syntax works in a particular PCRE implementation, that does not mean that it will work, nor is expected to work, on other PCRE libraries. Each PCRE implementation tends to have extensions and interpretation of standard variations that are different. Core behaviour should at a gross level be the same, but "???" is not a core behaviour so even if it is valid in a specific library I would not personally expect it to be supported in other PCRE libraries.

---

## Comment 6

> Username: jzmaddock  
> Created at: 2023-02-16 12:36:35 UTC  
> Url: https://github.com/boostorg/regex/issues/190#issuecomment-1433024097  

Tested with:  
  
```  
#include <iostream>  
#include <boost/regex.hpp>  
  
int main(int argc, char** argv)  
{  
   try  
   {  
      boost::regex e("((([A-Za-z]{3,9}:(?://)?)(?:[-;:&=+$,\\w]+@)?[A-Za-z0-9.-]+(:[0-9]+)?|(?:ww‌​w.|[-;:&=+$,\\w]+@)[A-Za-z0-9.-]+)((?:/[+~%/.\\w-]*)???(?:[-+=&;%@.\\w])#?‌​(?:[\\w]))?)");  
   }  
   catch (const boost::regex_error& e)  
   {  
      std::cout << e.what() << std::endl;  
   }  
}  
```  
  
Results in:  
  
```  
Invalid preceding regular expression prior to repetition operator.  The error occurred while parsing the regular expression fragment: '.\w-]*)???>>>HERE>>>(?:[-+=&;%'.  
```  
  
Which I believe is correct/expected behaviour: as discussed above the "???" sequence is an invalid regular expression and an exception is thrown.  *There is no program hang*.  
  
Testing with Perl online at https://ingram-braun.net/erga/online-regex-tester-perl-php-javascript/ also chokes on the nested ???'s.  
  
If I've misunderstood or you can provide a self contained test case please reopen.
