# #1 fix: narrow uses insufficient input char type [Merged]

> Username: DanielaE  
> Created at: 2013-12-30 17:27:37 UTC  
> Updated at: 2014-02-07 17:19:19 UTC  
> Merged at: 2014-02-07 03:23:53 UTC  
> Closed at: 2014-02-07 03:23:53 UTC  
> Url: https://github.com/boostorg/date_time/pull/1  

The current implementation uses std::stringstream::narrow(char) with its 'char' input parameter type to transform 'charT' chars taken from a std::basic_istream<charT> into 'char' chars. This is most likely not what is intended if 'charT' is wider than 'char'. Fix this by using std::basic_istream<charT>::narrow(charT) instead.  
  
Signed-off-by: Daniela Engert dani@ngrt.de

---

## Comment 1

> Username: Belcourt  
> Created_at: 2014-02-07 03:23:31 UTC  
> Url: https://github.com/boostorg/date_time/pull/1#issuecomment-34400940  

Looks good, checked with gcc-4.4 and gcc-4.8.2, no complaints, thanks!

---
