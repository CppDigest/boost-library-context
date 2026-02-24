# #129 - Whether to plan to support mysql5.0 and mysql5.5？ [Open]

> Username: iamhaiwen  
> Created at: 2023-03-21 05:00:44 UTC  
> Updated at: 2023-03-25 20:32:09 UTC  
> Url: https://github.com/boostorg/mysql/issues/129  



---

## Comment 1

> Username: anarthal  
> Created at: 2023-03-21 09:22:17 UTC  
> Url: https://github.com/boostorg/mysql/issues/129#issuecomment-1477505891  

Wow, these are really ancient versions of MySQL, and are not officially supported anymore by Oracle (so they likely contain vulnerabilities) - is there a reason you can't upgrade?

---

## Comment 2

> Username: anarthal  
> Created at: 2023-03-21 09:28:04 UTC  
> Url: https://github.com/boostorg/mysql/issues/129#issuecomment-1477513601  

As a note for development, supporting 5.5 requires, at least:  
* Adding a MySQL 5.5 CI. We can use https://hub.docker.com/r/cytopia/mysql-5.5 as a base  
* Running TLS tests with TLSv1.0 instead of the default TLS version, since that's what 5.5 seems to support  
* Add support for servers not having DEPRECATE EOF capabilities.  
* Add support for servers not having plugin auth lenenc data capabilities.  
  
I haven't studied MySQL 5.0.  
  
@githaiwen if you're stuck with any of these versions and this is something of interest to you, I can move this forward to try to make it to Boost 1.83. But please bear in mind that using such an old MySQL version is pretty bad for security.  
  
Regards,  
Ruben.

---

## Comment 3

> Username: iamhaiwen  
> Created at: 2023-03-21 10:59:13 UTC  
> Url: https://github.com/boostorg/mysql/issues/129#issuecomment-1477634154  

> 哇，这些真的是MySQL的古老版本，并且不再受到Oracle的正式支持（因此它们可能包含漏洞） - 有什么原因无法升级吗？  
  
There are internal servers that heavily use redhat5 or 6 systems, these systems offer older versions of mysql by default, and there is little incentive to upgrade mysql.

---

## Comment 4

> Username: iamhaiwen  
> Created at: 2023-03-21 10:59:29 UTC  
> Url: https://github.com/boostorg/mysql/issues/129#issuecomment-1477634505  

> As a note for development, supporting 5.5 requires, at least:  
>   
> * Adding a MySQL 5.5 CI. We can use https://hub.docker.com/r/cytopia/mysql-5.5 as a base  
> * Running TLS tests with TLSv1.0 instead of the default TLS version, since that's what 5.5 seems to support  
> * Add support for servers not having DEPRECATE EOF capabilities.  
> * Add support for servers not having plugin auth lenenc data capabilities.  
>   
> I haven't studied MySQL 5.0.  
>   
> @githaiwen if you're stuck with any of these versions and this is something of interest to you, I can move this forward to try to make it to Boost 1.83. But please bear in mind that using such an old MySQL version is pretty bad for security.  
>   
> Regards, Ruben.  
  
thanks

---

## Comment 5

> Username: anarthal  
> Created at: 2023-03-21 12:35:10 UTC  
> Url: https://github.com/boostorg/mysql/issues/129#issuecomment-1477763108  

I will try to prioritize this to get it done for Boost 1.83 then.
