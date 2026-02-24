# #174 Possible minimal fix for Trac #12016 [Closed]

> Username: sehe  
> Created at: 2016-02-23 22:33:40 UTC  
> Updated at: 2016-02-23 22:46:40 UTC  
> Closed at: 2016-02-23 22:44:19 UTC  
> Url: https://github.com/boostorg/spirit/pull/174  

https://svn.boost.org/trac/boost/ticket/12016  
  
It strikes me that moving from mapped values in a symbols trie is never a good idea, so the attribute value could just be marked const.  
  
> _I'm not sure in this suggestion whether `handles_container` needs to be using `T` (or apply `remove_const<>` again...). Perhaps the trait already correctly identifies const types as containers._

---

## Comment 1

> Username: djowel  
> Created_at: 2016-02-23 22:38:39 UTC  
> Url: https://github.com/boostorg/spirit/pull/174#issuecomment-187946138  

Beat you to a solution, sehe :-) See fix in develop (https://github.com/boostorg/spirit/commit/035fc0fd9d511d4819171dd9af3d740ba767ed00)  
  
Tell me if that works for you.   
  
PS> There are times when you want the data non-const (e.g. the nabialek trick).

---

## Comment 2

> Username: sehe  
> Created_at: 2016-02-23 22:42:56 UTC  
> Updated_at: 2016-02-23 22:43:08 UTC  
> Url: https://github.com/boostorg/spirit/pull/174#issuecomment-187947179  

@djowel  That's a lot more to the point yes.   
  
Why does the `move_to` "trait" move from lvalue references, though? Seems error-prone. I bet there is a good reason, but I assumed it would be preferable to perfect-forward rvalue-ness of all arguments?

---

## Comment 3

> Username: djowel  
> Created_at: 2016-02-23 22:45:06 UTC  
> Url: https://github.com/boostorg/spirit/pull/174#issuecomment-187947766  

Yes, I've been thinking about that as well (move from lvalue references). I need to investigate more on this.

---
