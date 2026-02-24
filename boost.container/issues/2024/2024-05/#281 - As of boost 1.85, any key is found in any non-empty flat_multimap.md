# #281 - As of boost 1.85, any key is found in any non-empty flat_multimap [Closed]

> Username: spth  
> Created at: 2024-05-30 20:12:19 UTC  
> Updated at: 2024-06-04 22:03:34 UTC  
> Closed at: 2024-06-04 22:03:34 UTC  
> Url: https://github.com/boostorg/container/issues/281  

Consider this simple test program:  
  
~~~~  
#include <iostream>  
#include <boost/container/flat_map.hpp>  
  
typedef boost::container::flat_multimap<int, short> operand_map_t;  
  
operand_map_t o;  
  
int main(void)  
{  
	o.insert(std::pair<int, short>(93, 48));  
	if (o.find(97) != o.end())  
		std::cout << "Boost is broken!\n";  
	return 0;  
}  
~~~~  
  
When using boost 1.85, and compiling with GCC 13.2.0 at -O2, I get the "Boost is broken!" output. I do not see the bug when using boost 1.83. I do not know if this is a GCC or boost bug. I found the bug on Debian GNU/Linux on amd64, when looking into https://sourceforge.net/p/sdcc/bugs/3739/. Since that bug was reported by an SDCC user, multiple people have already run into this problem.

---

## Comment 1

> Username: iv-m  
> Created at: 2024-05-31 10:21:15 UTC  
> Url: https://github.com/boostorg/container/issues/281#issuecomment-2141714261  

> Consider this simple test program  
  
First, this is obviously a strict aliasing problem, as adding `-fno-strict-aliasing` to the compiler command line makes the program function correctly. So, as is pointed in the sdcc bug, this may be related (or same issue) as https://github.com/boostorg/container/issues/252.  
  
`git bisect` with this example  as the test shows that 1a4a205ea6ef7b4e67a2faab7c7d745711807695 is the first broken commit. Indeed, reverting it on top of tag boost-1.85.0 (6e697d796897b32b471b4f0740dcaa03d8ee57cc) fixes this issue for me.

---

## Comment 2

> Username: igaztanaga  
> Created at: 2024-06-04 22:03:34 UTC  
> Url: https://github.com/boostorg/container/issues/281#issuecomment-2148487431  

Thanks for the report. Hopefully this problem was fixed in commit (post 1.85):  
  
https://github.com/boostorg/container/commit/20ad12f20e661978e90dc7f36d8ab8ac05e5a5a9  
  
I see that gentoo has applied a patch based on that commit resolving the issue.
