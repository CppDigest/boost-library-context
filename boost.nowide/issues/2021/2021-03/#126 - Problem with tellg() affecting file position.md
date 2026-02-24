# #126 - Problem with tellg() affecting file position [Closed]

> Username: willw-git  
> Created at: 2021-03-19 14:32:32 UTC  
> Updated at: 2021-03-24 10:17:50 UTC  
> Closed at: 2021-03-21 15:15:38 UTC  
> Url: https://github.com/boostorg/nowide/issues/126  

My test program:  
```  
#include <boost/nowide/fstream.hpp>  
#include <boost/nowide/iostream.hpp>  
  
int main()  
{  
	boost::nowide::fstream fs;  
	// Open file in text mode, to read  
	fs.open("test.txt", std::ios_base::in);  
	std::string line1, line2;  
	getline(fs, line1);  
	boost::nowide::cout << line1 << "\n";  
	auto tg = fs.tellg(); // This line causes trouble  
	getline(fs, line2);  
	boost::nowide::cout << line2 << "\n";  
}  
```  
My input file test.txt that looks like this  
```  
Line 1  
Line 2  
```  
The output of the program:  
  
```  
Line 1  
ine 2  
  
```  
i.e. the first character of the second line is missing.  
  
Removing the call to tellg() fixes the behaviour.  
  
I believed that tellg() was non-destructive on file position. Am I wrong?  
  
Platform = Windows 10.0.19042  
Compiler = Visual C++ 2019 compiling to 32-bit  
Boost 1.75

---

## Comment 1

> Username: Flamefire  
> Created at: 2021-03-20 13:13:15 UTC  
> Url: https://github.com/boostorg/nowide/issues/126#issuecomment-803316745  

I can confirm that and am working on a fix. The issue is related to newlines after the current read position, fixing this is a bit complicated, I'll see what I can do without sacrificing to much performance

---

## Comment 2

> Username: willw-git  
> Created at: 2021-03-22 08:52:14 UTC  
> Url: https://github.com/boostorg/nowide/issues/126#issuecomment-803882996  

Thanks for super-fast  response!  
I'll try plugging the patch into the code where I detected the error originally.

---

## Comment 3

> Username: Flamefire  
> Created at: 2021-03-22 08:55:03 UTC  
> Url: https://github.com/boostorg/nowide/issues/126#issuecomment-803884889  

The next Boost release is due and as that is a quite serious bug I wanted to get it in, so you'll be able to use 1.76  
Feel free to report whether everything now works for you  
  
Also note that read performance of text-mode streams is degraded by that fix, this will be fixed later but requires some bigger changes which are impossible to do at this stage into the Boost release process

---

## Comment 4

> Username: willw-git  
> Created at: 2021-03-24 10:17:50 UTC  
> Url: https://github.com/boostorg/nowide/issues/126#issuecomment-805681599  

Seems to work fine now - thanks!
