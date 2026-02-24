# #25 - Boost.Chrono testsuite triggers narrowing warnings [Closed]

> Username: DanielaE  
> Created at: 2017-05-03 07:28:05 UTC  
> Updated at: 2017-05-05 06:16:31 UTC  
> Closed at: 2017-05-05 06:16:31 UTC  
> Url: https://github.com/boostorg/chrono/issues/25  

The following small code is a minimal excerpt from the testsuite which exhibits the problem. AFAICS there is no way for users to avoid these warnings.  
  
Compile the program f.e. using `cl /c /EHsc /W4 test.cpp` to generate the warnings as shown in [PR#24](https://github.com/boostorg/chrono/pull/24)  
```  
#include <boost/chrono.hpp>  
#include <sstream>  
  
using Clock = boost::chrono::high_resolution_clock;  
using Duration = boost::chrono::hours;  
using TimePoint = boost::chrono::time_point<Clock, Duration>;  
  
void test1(const char * s) {  
  std::istringstream in(s);  
  Duration d;  
  in >> d;  
}  
  
void test2(const char * s) {  
  std::istringstream in(s + boost::chrono::clock_string<Clock, char>::since());  
  TimePoint tp;  
  in >> tp;  
}  
  
int main() {  
  test1("5000 hours");  
  test2("5000 hours");  
  return 0;  
}  
```

---

## Comment 1

> Username: viboes  
> Created at: 2017-05-03 11:22:15 UTC  
> Updated at: 2017-05-03 11:23:12 UTC  
> Url: https://github.com/boostorg/chrono/issues/25#issuecomment-298884487  

Ok, I see the use case. When doing input from a stream the user is unable to do any specific cast.  
The problem for me is not on either duration or time_point classes but on the stream duration extractor.   
  
I believe  that it is there where we need the static_cast.  
  
Could you rework the PR?
