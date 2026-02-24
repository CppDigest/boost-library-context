# #58 - Unexpected behavior when using algorithm::trim() to remove spaces [Open]

> Username: rosenrodt  
> Created at: 2019-01-28 03:20:53 UTC  
> Updated at: 2019-01-30 03:37:11 UTC  
> Url: https://github.com/boostorg/algorithm/issues/58  

The issue comes from boostorg/compute#817 where `boost::trim()` is invoked to sanitize OpenCL kernel arguments from preprocessor-generated strings. One of the failing cases taken directly from the boost compute repo is this:  
  
```c++  
two, pi          // original string     
[two, pi]        // vector after split() using comma as delimiter  
two              // first element after trim()  
 p               // second element after trim()  
```  
Here the `trim()` actually leave a leading space left of `pi` and slices one valid character on the right. One big BUT: this only happens to Linux cross-compiler targeting Android platform, downloaded from Android NDK page. Where as MSVC on Windows, GCC/Clang on Mac all run without problems. What might be the reason of this unexpected behavior?

---

## Comment 1

> Username: mclow  
> Created at: 2019-01-28 05:53:09 UTC  
> Url: https://github.com/boostorg/algorithm/issues/58#issuecomment-458006587  

Since you didn't provide an actual test program, I wrote my own:  
```  
#include <iostream>  
#include <boost/algorithm/string/split.hpp>  
#include <boost/algorithm/string/trim.hpp>  
  
namespace ba = boost::algorithm;  
  
int main ()  
{  
	std::string s("two, pi");  
	std::vector<std::string> v;  
	ba::split(v, s, ba::is_any_of(","));  
	for (auto const &st : v)   
		std::cout << "'" << st << "'" << std::endl;  
	std::cout << "-----" << std::endl;  
	for (auto st : v)  
	{  
		ba::trim(st);  
		std::cout << "'" << st << "'" << std::endl;  
	}  
}  
```  
  
```  
$ clang++ -std=c++14 -I $BOOST boost.cpp && ./a.out   
'two'  
' pi'  
-----  
'two'  
'pi'  
```  
  
That looks fine to me.    
> One big BUT: this only happens to Linux cross-compiler targeting Android platform, downloaded from Android NDK page   
  
Can you run this program on your platform and tell me what happens?  
Also, what version of Boost are you using?

---

## Comment 2

> Username: rosenrodt  
> Created at: 2019-01-29 03:06:07 UTC  
> Url: https://github.com/boostorg/algorithm/issues/58#issuecomment-458390081  

Thanks! That I did not providing a minimal example was lazy on my part.    
  
Here is the result of the test program you provided:  
  
```  
export compile=/mnt/d/tools/android/android_toolchain_linux/bin/aarch64-linux-android-clang++  
  
$ $compile -std=c++14 -I /mnt/d/repo/boost_1_68_0 src/test.cpp  
$ adb push a.out data/local && adb shell ./data/local/a.out  
a.out: 1 file pushed. 8.8 MB/s (223432 bytes in 0.024s)  
'two'  
' pi'  
-----  
'two'  
' p'  
```  
  
The Boost version I have is 1.68.0. Compiled with Android cross compiler under Windows Linux Subsystem

---

## Comment 3

> Username: mclow  
> Created at: 2019-01-30 03:37:11 UTC  
> Url: https://github.com/boostorg/algorithm/issues/58#issuecomment-458799845  

The only difference that I can see is that you're using Boost 1.68.0, and I'm using trunk.  
However, I don't think that code has changed since before 1.68.0 (checks - nope)  
  
That's very odd. And the fact that it only fails when cross-compiling to ARM suggests a tool problem.  Doesn't rule out a bug in Boost, though.
