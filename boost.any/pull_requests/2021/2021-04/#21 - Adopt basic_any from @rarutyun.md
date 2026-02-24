# #21 Adopt basic_any from @rarutyun [Merged]

> Username: apolukhin  
> Created at: 2021-04-06 17:42:13 UTC  
> Updated at: 2021-04-20 07:19:08 UTC  
> Merged at: 2021-04-17 15:10:26 UTC  
> Closed at: 2021-04-17 15:10:26 UTC  
> Url: https://github.com/boostorg/any/pull/21  



---

## Comment 1

> Username: pdimov  
> Created_at: 2021-04-17 16:45:52 UTC  
> Url: https://github.com/boostorg/any/pull/21#issuecomment-821851906  

Don't use a subdirectory that doesn't match the repo name, please. This makes all headers exceptions for boostdep.

---

## Comment 2

> Username: apolukhin  
> Created_at: 2021-04-17 18:23:05 UTC  
> Url: https://github.com/boostorg/any/pull/21#issuecomment-821866071  

> Don't use a subdirectory that doesn't match the repo name, please.  
  
Fixing...

---

## Review 3 [Commented]

> Username: rarutyun  
> Created_at: 2021-04-18 17:22:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/any/pull/21#pullrequestreview-638345795  

📁 doc/any.xml

```diff
 916 |+           <purpose><simpara>Custom keyword cast for extracting a value
 917 |+           of a given type from an
 918 |+           <code><classname>basic_any</classname></code>. Also awailable from <code>boost</code> namespace.</simpara></purpose>
```

> Username: rarutyun  
> Created_at: 2021-04-18 16:17:30 UTC  
> Updated_at: 2021-04-18 17:22:17 UTC  
> Url: https://github.com/boostorg/any/pull/21#discussion_r615420867  

awailable -> available

> Username: apolukhin  
> Created_at: 2021-04-20 07:11:11 UTC  
> Url: https://github.com/boostorg/any/pull/21#discussion_r616403964  

fixed

---

📁 doc/any.xml

```diff
 600 |+               <code>OptimizeForSize</code> and <code>OptimizeForAlignment</code> template parameters set to
 601 |+               typical/maximal size and alignment of types respectively. Memory allocation would be avoided
 602 |+               for storing nothrow move constructible types with size and alignment less or
```

> Username: rarutyun  
> Created_at: 2021-04-18 16:27:02 UTC  
> Updated_at: 2021-04-18 17:22:17 UTC  
> Url: https://github.com/boostorg/any/pull/21#discussion_r615422038  

Small comment. As far as I remember they say "less **than** or equal to"

> Username: apolukhin  
> Created_at: 2021-04-20 07:11:37 UTC  
> Updated_at: 2021-04-20 07:11:38 UTC  
> Url: https://github.com/boostorg/any/pull/21#discussion_r616404286  

fixed

---

📁 doc/any.xml

```diff
 597 |+               <para>There are certain applications that require <code><classname>boost::any</classname></code>
 598 |+               functionality, do know the typical/maximal size of the stored object and wish to avoid dynamic memory
 599 |+               allocation overhead. Such applications should make a typedef for <code>basic_any</code> with the
```

> Username: rarutyun  
> Created_at: 2021-04-18 16:46:17 UTC  
> Updated_at: 2021-04-18 17:22:17 UTC  
> Url: https://github.com/boostorg/any/pull/21#discussion_r615424410  

I wouldn't say _should_ here. It seems as advice how to act. But the largest type might be too large and bring big stack memory consumption.  
  
I think I would modify it something like: "For the convenience such applications may create a typedef for <code>basic_any</code> with the <code>OptimizeForSize</code> and <code>OptimizeForAlignment</code> template parameters set to typical/maximal size and alignment of types respectively or introduce multiple aliases to <code>basic_any</code> to manage type groups

> Username: apolukhin  
> Created_at: 2021-04-20 07:19:08 UTC  
> Url: https://github.com/boostorg/any/pull/21#discussion_r616411244  

Fixed the beginning, without adding the "or introduce multiple aliases to basic_any to manage type groups". We gave no description of the "type groups". The sentence also becomes over-complicated with that addition.

---

📁 doc/any.xml

```diff
 918 |+           <code><classname>basic_any</classname></code>. Also awailable from <code>boost</code> namespace.</simpara></purpose>
 919 |+ 
 920 |+           <returns><simpara> If passed a pointer, it returns a
```

> Username: rarutyun  
> Created_at: 2021-04-18 17:16:07 UTC  
> Updated_at: 2021-04-18 17:22:17 UTC  
> Url: https://github.com/boostorg/any/pull/21#discussion_r615427867  

Suggestion. I would reformulate to something like that.  
  
First part: "**For overloads with pointer to ``basic_any``:** return a similarly qualified pointer..." _return_ is intentionally without **s** at the end because we have multiple overloads  
  
Second part: "**For overloads with reference to ``basic_any``:** If T is ValueType, **return** a copy of the held value, otherwise, if T is a reference **return** a reference to the held value"

> Username: apolukhin  
> Created_at: 2021-04-20 07:14:20 UTC  
> Url: https://github.com/boostorg/any/pull/21#discussion_r616407517  

Those are the copy-pasted words of the original library author. I'd rather leave them as is.

---

📁 doc/any.xml

```diff
 929 |+           throw; overloads taking an
 930 |+           <code><classname>basic_any</classname></code> value or reference
 931 |+           throws <code><classname>bad_any_cast</classname></code> if
```

> Username: rarutyun  
> Created_at: 2021-04-18 17:16:34 UTC  
> Updated_at: 2021-04-18 17:22:17 UTC  
> Url: https://github.com/boostorg/any/pull/21#discussion_r615427923  

throws -> throw

> Username: apolukhin  
> Created_at: 2021-04-20 07:12:47 UTC  
> Url: https://github.com/boostorg/any/pull/21#discussion_r616405412  

fixed


---
