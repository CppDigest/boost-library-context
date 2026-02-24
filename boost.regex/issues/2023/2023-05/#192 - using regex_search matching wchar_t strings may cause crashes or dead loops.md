# #192 - using regex_search matching wchar_t strings may cause crashes or dead loops [Closed]

> Username: 951193102  
> Created at: 2023-05-01 08:09:00 UTC  
> Updated at: 2024-03-27 10:56:14 UTC  
> Closed at: 2024-03-27 10:56:14 UTC  
> Url: https://github.com/boostorg/regex/issues/192  

wchar_t * test = L"11111";    
const wchar_t *  matchTestStart = (const wchar_t*)test;  
	const wchar_t *  matchTestEnd = (const wchar_t*)((char*)test + sizeof(test) + 1);  
	while (boost::regex_search(wmatchTestStart,wmatchTestEnd, what, reg)) {  
		printf("%ws", (what[0]));  
	}

---

## Comment 1

> Username: jzmaddock  
> Created at: 2023-05-01 16:50:12 UTC  
> Url: https://github.com/boostorg/regex/issues/192#issuecomment-1529939561  

Please post a self contained example that actually compilers - for example what is the actual expression used?

---

## Comment 2

> Username: 951193102  
> Created at: 2023-05-08 01:32:29 UTC  
> Url: https://github.com/boostorg/regex/issues/192#issuecomment-1537618111  

I don't quite understand what you're saying,The problem is that the wchar_t type memory length is not aligned.

---

## Comment 3

> Username: stefankueng  
> Created at: 2023-06-05 17:38:04 UTC  
> Url: https://github.com/boostorg/regex/issues/192#issuecomment-1577206059  

have the same (or similar) problem:  
  
```  
std::wstring fileString = L"abcdefghijklmnopqrstuvwxyz";  
auto start              = fileString.cbegin();  
auto end                = fileString.cend();  
boost::match_results<std::wstring::const_iterator> whatC;  
boost::match_flag_type flags = boost::match_default | boost::format_all | boost::match_not_dot_newline;  
int ft = boost::regbase::icase;  
boost::wregex  searchRe = boost::wregex(L"^test", ft);  
int nFound = 0;  
while (boost::regex_search(start, end, whatC, searchRe, flags))  
{  
    nFound++;  
}  
  
```  
  
The problem appears to be the '^' in the search string. This results in the iterator being decremented before begin().  
  
compiled with MSVC (Visual Studio 17.6) and boost 1.82.0

---

## Comment 4

> Username: lifenjoiner  
> Created at: 2024-02-14 08:12:44 UTC  
> Updated at: 2024-02-15 23:54:51 UTC  
> Url: https://github.com/boostorg/regex/issues/192#issuecomment-1943263047  

> The problem appears to be the '^' in the search string. This results in the iterator being decremented before begin().  
  
Also traced into this, out of range:  
https://github.com/boostorg/regex/blob/237e69caf65906d0313c9b852541b07fa84a99c1/include/boost/regex/v5/perl_matcher_common.hpp#L372-L375  
  
...  
Finally, found the `match_prev_avail` flag was set accidentally...

---

## Comment 5

> Username: jzmaddock  
> Created at: 2024-03-25 18:32:31 UTC  
> Url: https://github.com/boostorg/regex/issues/192#issuecomment-2018651302  

```  
   while (boost::regex_search(start, end, whatC, searchRe, flags))  
   {  
      nFound++;  
   }  
```  
  
This *will* infinite loop since `start` is never updated in the loop body.  That aside this works fine for me.  
  
> Finally, found the match_prev_avail flag was set accidentally...  
  
So is this still an issue or not?

---

## Comment 6

> Username: lifenjoiner  
> Created at: 2024-03-26 00:45:02 UTC  
> Url: https://github.com/boostorg/regex/issues/192#issuecomment-2019188259  

> > Finally, found the match_prev_avail flag was set accidentally...  
>   
> So is this still an issue or not?  
  
No for https://github.com/stefankueng/grepWin/issues/405, where I came from, at least.  
  
> That aside this works fine for me.  
  
Me too.

---

## Comment 7

> Username: jzmaddock  
> Created at: 2024-03-27 10:56:14 UTC  
> Url: https://github.com/boostorg/regex/issues/192#issuecomment-2022473379  

Please reopen if someone can post a reproducer.
