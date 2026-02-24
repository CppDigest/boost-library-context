# #89 - Unexpected result of parsing "-1" to safe<unsigned> [Closed]

> Username: akrzemi1  
> Created at: 2020-10-14 11:43:22 UTC  
> Updated at: 2020-10-17 04:10:01 UTC  
> Closed at: 2020-10-17 04:10:01 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/89  

[This example](https://www.boost.org/doc/libs/1_74_0/libs/safe_numerics/doc/html/tutorial/7.html) in the docs (Checking of Input Values Can Be Easily Overlooked) builds an expectation that the library would signal a problem (throw an exception) in the following example:  
  
```c++  
#include <iostream>  
#include <sstream>  
#include <boost/safe_numerics/safe_integer.hpp>  
  
int main()   
try {  
  boost::safe_numerics::safe<unsigned> u;  
  std::istringstream is{"-1"};  
  is >> u;  
  std::cout << u << std::endl;  
}  
catch (std::exception const& e)  
{  
  std::cerr << "ERR: " << e.what() << std::endl;  
}  
```  
  
But instead, this program outputs a huge positive value.  
  
It is either a bug in the implementation, or an expected result in the programming model adapted by this library. If it is the latter, I expect that the library indicates it explicitly in the docs.

---

## Comment 1

> Username: robertramey  
> Created at: 2020-10-14 14:22:26 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/89#issuecomment-708437000  

looks like a bug in the library implementation to me.  Looking into it.

---

## Comment 2

> Username: robertramey  
> Created at: 2020-10-15 04:10:58 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/89#issuecomment-708884997  

progress report.  
  
running this through the debugger leads me to conclude that it fails due to an implementation error in the clang (Xcode version) io library.  reading "-1" into an unsigned does not set the fail bit as I would expect from https://en.cppreference.com/w/cpp/io/ios_base/iostate which says:  
  
The failbit  
The failbit is set by the following standard library functions:  
...  
"The **numeric**, pointer, and boolean input overloads of basic_istream::operator>> (technically, the overloads of num_get::get they call), if the input cannot be parsed as a valid value or if the value parsed does not fit in the destination type."  
  
Since "-1" cannot be parsed as a valid value for an unsigned type, it num_get::get should return with the failbit set.  It does not.  
  
I'll see if I can find a way to work around it.  
  
Any other observations/ideas would be welcome.

---

## Comment 3

> Username: akrzemi1  
> Created at: 2020-10-15 06:23:05 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/89#issuecomment-708926926  

For some background information, the C++ IOSteams library is known to allow parsing negative numbers to unsigned types. This is "consistent" with other parts, like:  
  
```c++  
unsigned u = -1; // fine, and in fact common  
```  
  
Boost.LexicalCast inherits it:  
  
```c++  
unsigned u = boost::lexical_cast<unsigned>("-1"); // never throws  
```   
  
Some other conversion functions in C++ also allow negative numbers in this case:  
  
```c++  
int main()  
{  
    const char * s = "-1";  
    char * end;  
    auto u = std::strtoul(s, &end, 10);  
    std::cout << u; // also outputs a huge positive  
}  
```  
  
I guess libraries that address it, reimplement the parsing from the scratch, rather than relying on IOStreams.  
  
In C++17 we have `std::from_chars` that works correctly:  
  
```c++  
#include <system_error>  
#include <charconv>  
  
int main()  
{  
    std::string s = "-1";  
    unsigned u;  
    auto [ptr, ec] = std::from_chars(s.data(), s.data() + s.size(), u);  
    if (ptr != s.data() + s.size())   
        throw std::system_error(make_error_code(ec));  
}  
```  
  
But I do not know if you can afford to use C++17?

---

## Comment 4

> Username: robertramey  
> Created at: 2020-10-15 15:31:47 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/89#issuecomment-709405929  

>unsigned u = -1; // fine, and in fact common  
This is inexplicable to me.  Actually all the examples you cite I would consider bugs in the library.  But I guess if everyone does it its not a bug.  
  
The fundamental premise of the safe numerics library is that C++ numerical expressions should never produce a result which is different than the corresponding expression in integer (and later float) arithmetic.  So this is one more case that the safe numerics library should address.  Once this is done, It will result in another pathological example in the tutorial.  Very very good information on your part.  It amazes me that I can work with C++ 25 years, be author of the safe numerics library and not be aware of this.  When I think of C++ being used on things like the Boeing 737 flight controller software (which has to be rebooted every 50 hrs to address ?memory leaks or?) it makes me not want to fly.  
  
I would be reluctant to bump the library requirement up to C++17 just to workaround this one small problem.  I'm guessing I'll make my own version of num_get or ? which doesn't exhibit this behavior.  The research will be a pain but the actual work shouldn't be much.  
  
One thing that I haven't done is to look into the standard text to see what it says about the issue.  Given that everyone seems to be OK with the current behavior - I'll guess that the standard permits it.  
  
You have been incredibly helpful in all this.  I'll add a comment here when I've got it fixed and uploaded.

---

## Comment 5

> Username: robertramey  
> Created at: 2020-10-17 04:09:35 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/89#issuecomment-710743776  

fixed - closing this - feel free to re-open if necessary.
