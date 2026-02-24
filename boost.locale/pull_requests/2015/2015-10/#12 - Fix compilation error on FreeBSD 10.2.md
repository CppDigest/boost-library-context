# #12 Fix compilation error on FreeBSD 10.2. [Closed]

> Username: Flast  
> Created at: 2015-10-12 11:06:55 UTC  
> Updated at: 2015-10-14 04:37:54 UTC  
> Closed at: 2015-10-13 22:53:03 UTC  
> Url: https://github.com/boostorg/locale/pull/12  

Since r233600[1], BSD libc requires that include xlocale.h in the correct  
order[2].  
1. http://lists.freebsd.org/pipermail/svn-src-head/2012-March/035792.html  
2. https://www.freebsd.org/cgi/man.cgi?query=xlocale&apropos=0&sektion=3&manpath=FreeBSD+10.2-RELEASE&arch=default&format=html

---

## Comment 1

> Username: Flast  
> Created_at: 2015-10-12 11:17:28 UTC  
> Url: https://github.com/boostorg/locale/pull/12#issuecomment-147368291  

NOTE: In libc++, `<locale>` will include `<xlocale.h>` implicitly.

---

## Comment 2

> Username: artyom-beilis  
> Created_at: 2015-10-12 11:27:52 UTC  
> Url: https://github.com/boostorg/locale/pull/12#issuecomment-147369620  

Thanks now I understand why it worked on my FreeBSD  
Commited your change Artyom Beilis -------------- CppCMS - C++ Web Framework:   http://cppcms.com/ CppDB - C++ SQL Connectivity: http://cppcms.com/sql/cppdb/  
  
```  
  From: Kohei Takahashi <notifications@github.com>  
```  
  
 To: boostorg/locale locale@noreply.github.com   
 Sent: Monday, October 12, 2015 2:17 PM  
 Subject: Re: [locale] Fix compilation error on FreeBSD 10.2. (#12)  
  
NOTE: In libc++, <locale> will include <xlocale.h> implicitly. —  
Reply to this email directly or view it on GitHub.

---

## Comment 3

> Username: Flast  
> Created_at: 2015-10-13 22:53:03 UTC  
> Url: https://github.com/boostorg/locale/pull/12#issuecomment-147876397  

Fixed by ca4a6265a10a469561ec78350e7eda37cf2b225c.

---

## Comment 4

> Username: artyom-beilis  
> Created_at: 2015-10-14 04:37:54 UTC  
> Url: https://github.com/boostorg/locale/pull/12#issuecomment-147933328  

Why the test on development branch is still failing? Artyom Beilis -------------- CppCMS - C++ Web Framework:   http://cppcms.com/ CppDB - C++ SQL Connectivity: http://cppcms.com/sql/cppdb/  
  
```  
  From: Kohei Takahashi <notifications@github.com>  
```  
  
 To: boostorg/locale locale@noreply.github.com   
Cc: Artyom Beilis artyomtnk@yahoo.com   
 Sent: Wednesday, October 14, 2015 1:53 AM  
 Subject: Re: [locale] Fix compilation error on FreeBSD 10.2. (#12)  
  
Closed #12.—  
Reply to this email directly or view it on GitHub.

---
