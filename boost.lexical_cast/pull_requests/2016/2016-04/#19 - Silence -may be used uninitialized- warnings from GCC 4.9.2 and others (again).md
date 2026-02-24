# #19 Silence "may be used uninitialized" warnings from GCC 4.9.2 and others (again). [Merged]

> Username: plopresti  
> Created at: 2016-04-01 17:23:23 UTC  
> Updated at: 2016-04-01 19:13:40 UTC  
> Merged at: 2016-04-01 19:12:29 UTC  
> Closed at: 2016-04-01 19:12:29 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/19  

Avoid boost::value_initialized<> since it does not work on move-only types (see https://svn.boost.org/trac/boost/ticket/11570 and https://github.com/boostorg/lexical_cast/commit/424320d9a18b17c55c86012673722ca1b42dcfa9).

---

## Comment 1

> Username: plopresti  
> Created_at: 2016-04-01 17:35:43 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/19#issuecomment-204482982  

Note that the various compiler issues with value-initialization (http://www.boost.org/doc/libs/1_60_0/libs/utility/value_init.htm#compiler_issues) should not matter here, since the goal is to silence the warning and not actually to initialize the variable.

---

## Comment 2

> Username: apolukhin  
> Created_at: 2016-04-01 19:13:40 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/19#issuecomment-204528330  

Great thanks for the patch!

---
