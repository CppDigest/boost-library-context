# #358 - include process v2 header file cause compile failed in aix platfrom [Closed]

> Username: maplness  
> Created at: 2024-03-19 11:47:38 UTC  
> Updated at: 2024-10-25 00:13:55 UTC  
> Closed at: 2024-10-25 00:13:55 UTC  
> Url: https://github.com/boostorg/process/issues/358  

i include boost/process/v2/process.hpp ,cause a compiler error , compiler complain about #error "Platform not supported",  
i checked about code in boost/process/v2/pid.hpp and found about there is no _AIX macro defined, and i searched information on internet , there is few information for that. And official document also not talking about aix.  
i just curious that  is it really not supported for aix?
