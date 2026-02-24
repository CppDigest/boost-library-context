# #63 - charconv (from_chars) converter not work (crash) [Closed]

> Username: heretic13  
> Created at: 2024-03-15 19:08:30 UTC  
> Updated at: 2024-04-16 21:51:17 UTC  
> Closed at: 2024-04-16 21:51:16 UTC  
> Url: https://github.com/boostorg/convert/issues/63  

Hello.  
  
Boost-1.84  
OS Windows 11.  
Microsoft Visual Studio 2022 (Version 17.9.3)   
  
Sample program:  
`  
#include <iostream>  
#include <boost/convert.hpp>  
  
// If I comment line below, program not compiled  
#include <boost/make_default.hpp>  
#include <boost/convert/charconv.hpp>  
  
int main()  
{  
	auto cnv4 = boost::cnv::charconv();  
  
	std::string PuNum = "2";  
  
	const auto optPuNum = boost::convert<size_t>(PuNum, cnv4);  
  
	if (optPuNum)  
		std::cout << *optPuNum << "\n";  
	else  
		std::cout << "nullopt\n";  
  
	return 0;  
}  
`  
If I comment out the line "#include <boost/make_default.hpp>" the program will not compile.  
The use of this header is not obvious.  
  
This program crashes with an error when calling boost::convert<size_t>().

---

## Comment 1

> Username: yet-another-user  
> Created at: 2024-03-15 19:38:07 UTC  
> Updated at: 2024-03-15 20:56:43 UTC  
> Url: https://github.com/boostorg/convert/issues/63#issuecomment-2000334576  

Dvir Yitzchaki, do you think you might have a look at what the problem is? It seems related to your charconv.hpp. Although the actual problem might be in the overall infrastructure. Please let me know if you are in a position to have a look what the issue is. Tnx.

---

## Comment 2

> Username: yet-another-user  
> Created at: 2024-03-15 20:31:04 UTC  
> Updated at: 2024-03-15 21:32:38 UTC  
> Url: https://github.com/boostorg/convert/issues/63#issuecomment-2000400864  

Dvir, I had a quick look at the code.  
1. In charconv.hpp there is a boost::make_default() call. So,   
#include <boost/make_default.hpp>  
is needed and currently missing in charconv.hpp.  
2. Not immediately sure but "&\*range.end()" might be problematic as I suspect end() iterator is not necessarily dereferenceable (i.e. can't call op\*()). If so, then for non-empty strings &*range.end() will probably need to be replaced with "&*range.begin() + range.size()".  See, for ex., https://stackoverflow.com/questions/61669762/can-i-dereference-stdstring-end  
Tnx.

---

## Comment 3

> Username: yet-another-user  
> Created at: 2024-03-15 20:38:03 UTC  
> Url: https://github.com/boostorg/convert/issues/63#issuecomment-2000408913  

Hello, heretic13. :-) If you are in a rush you might consider replacing the boost::cnv::charconv converter with, say, boost::cnv::strtol to keep going while we are looking into the problem. Tnx.

---

## Comment 4

> Username: heretic13  
> Created at: 2024-03-15 20:53:57 UTC  
> Url: https://github.com/boostorg/convert/issues/63#issuecomment-2000426561  

Thank you. I replaced it with boost::cnv::spirit for better performance.

---

## Comment 5

> Username: yet-another-user  
> Created at: 2024-03-16 02:22:12 UTC  
> Updated at: 2024-03-16 05:37:06 UTC  
> Url: https://github.com/boostorg/convert/issues/63#issuecomment-2001181964  

Fixed it. Well, I believe I fixed it. Copied the problematic code from the original post to test/issue-64.cpp, compiled and ran. Merging the changes into the boost/convert/develop branch and will be propagating to master shortly after all tests passed (expected). If all goes well, then will be closing the ticket. Thank you for raising it. Much appreciated.

---

## Comment 6

> Username: dvirtz  
> Created at: 2024-03-18 00:33:40 UTC  
> Url: https://github.com/boostorg/convert/issues/63#issuecomment-2002684723  

@yet-another-user I see you closed you're PR.   
Do you want me to open one?

---

## Comment 7

> Username: yet-another-user  
> Created at: 2024-03-18 01:14:38 UTC  
> Url: https://github.com/boostorg/convert/issues/63#issuecomment-2002708085  

Dvir, the fix seemed fairly straightforward. I was not sure of your current situation. So, I went ahead and made the changes myself. Hope you don't mind. Will check it into the develop branch tonight. Feel free to take over from there if needs be. Tnx for the update.

---

## Comment 8

> Username: yet-another-user  
> Created at: 2024-04-16 21:51:16 UTC  
> Url: https://github.com/boostorg/convert/issues/63#issuecomment-2059977402  

Merged into the master branch. Should be in the next release.
