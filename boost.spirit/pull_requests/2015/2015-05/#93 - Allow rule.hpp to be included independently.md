# #93 Allow rule.hpp to be included independently. [Merged]

> Username: ahmedcharles  
> Created at: 2015-05-16 20:26:36 UTC  
> Updated at: 2015-05-17 00:32:19 UTC  
> Merged at: 2015-05-17 00:32:19 UTC  
> Closed at: 2015-05-17 00:32:19 UTC  
> Url: https://github.com/boostorg/spirit/pull/93  

Have rexpr include rule.hpp independently as a check.

---

## Comment 1

> Username: djowel  
> Created_at: 2015-05-16 22:58:02 UTC  
> Url: https://github.com/boostorg/spirit/pull/93#issuecomment-102709946  

In X3, my inclination is to simplify the include with only one include: #include <boost/spirit/home/x3.hpp>. Do you find that the situation causes a real slowdown? If so, I'd like to see some numbers on the compilation speed of both independent and single include. Most users will include most of X3 anyway.

---

## Comment 2

> Username: ahmedcharles  
> Created_at: 2015-05-16 23:31:10 UTC  
> Url: https://github.com/boostorg/spirit/pull/93#issuecomment-102711524  

That makes sense and I don't have empirical evidence that suggests that it isn't viable. Though, I imagine that eventually, someone is going to want to only #include <boost/spirit/home/x3/nonterminal/rule.hpp> rather than all of X3, especially since one of the goals of having the rule separation is improved compile times.  
  
And I'm a big fan of every header including it's dependencies.

---

## Comment 3

> Username: djowel  
> Created_at: 2015-05-16 23:38:26 UTC  
> Url: https://github.com/boostorg/spirit/pull/93#issuecomment-102711714  

Agreed. Perhaps I can provide docs for those who want independent includes. For the tutorials though, I'd go with a single include for ease of use.

---

## Comment 4

> Username: ahmedcharles  
> Created_at: 2015-05-17 00:03:07 UTC  
> Url: https://github.com/boostorg/spirit/pull/93#issuecomment-102712707  

I understand the objection now. This change now still uses the general include for the example, but allows rule.hpp to be included while also compiling.

---
