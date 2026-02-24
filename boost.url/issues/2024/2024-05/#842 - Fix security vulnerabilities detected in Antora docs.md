# #842 - Fix security vulnerabilities detected in Antora docs [Closed]

> Username: alandefreitas  
> Created at: 2024-05-14 23:49:46 UTC  
> Updated at: 2024-08-16 16:51:15 UTC  
> Closed at: 2024-08-16 16:51:15 UTC  
> Url: https://github.com/boostorg/url/issues/842  

[Boost.org organization](https://github.com/boostorg)  
  
Warning!	  
  
[boostorg / url](https://github.com/boostorg/url)  
  
Known security vulnerabilities detected  
  
Dependency  
xmldom  
Version  
<= 0.6.0	  
Defined in  
package-lock.json  
Vulnerabilities  
[Review all vulnerable dependencies](https://github.com/boostorg/url/security/dependabot)

---

## Comment 1

> Username: alandefreitas  
> Created at: 2024-05-24 03:30:01 UTC  
> Url: https://github.com/boostorg/url/issues/842#issuecomment-2128454354  

Unfortunately, xmldom has been in version 0.6.0 for 3 years: https://www.npmjs.com/package/xmldom  
  
There's no solution to that but to rewrite the functionality with another library.
