# #213 Fix: replace assertion by exception [Merged]

> Username: mkaravel  
> Created at: 2015-02-09 13:54:32 UTC  
> Updated at: 2015-02-11 13:07:22 UTC  
> Merged at: 2015-02-11 13:04:36 UTC  
> Closed at: 2015-02-11 13:04:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/213  

The assertion failure that is replaced indicates that something went wrong in the turns' computation, and more precisely that the turns computed are not consistent; such a case should better be handled by throwing an exception rather than an assertion.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2015-02-09 19:25:27 UTC  
> Updated_at: 2015-02-09 20:56:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/213#discussion_r24356655  

m_message (btw it seems const, you can probably also just return it in what())

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2015-02-09 19:28:42 UTC  
> Updated_at: 2015-02-09 20:56:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/213#discussion_r24356967  

OK for me.  
We might think of a macro which implements this behaviour in a sort of generic way, for example  
  
```  
BOOST_GEOMETRY_THROW_OR_ASSERT(enter_count == 0,   
  inconsistent_turns_exception,   
  "Enter count: " << enter_count);  
```  
  
so the third argument is a streamable thing part of the exception

---

## Comment 3

> Username: mkaravel  
> Created_at: 2015-02-09 20:57:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/213#discussion_r24364518  

Done as suggested.

---

## Comment 4

> Username: mkaravel  
> Created_at: 2015-02-09 20:58:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/213#discussion_r24364623  

I like the idea. Will work on it on another PR.

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2015-02-09 21:22:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/213#issuecomment-73592753  

I'm OK with merge

---
