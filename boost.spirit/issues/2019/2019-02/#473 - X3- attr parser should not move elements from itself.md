# #473 - X3: attr parser should not move elements from itself [Closed]

> Username: Kojoley  
> Created at: 2019-02-27 20:32:22 UTC  
> Updated at: 2019-02-28 12:22:07 UTC  
> Closed at: 2019-02-28 12:21:58 UTC  
> Url: https://github.com/boostorg/spirit/issues/473  

The second and subsequent calls to it will produce garbage on complex types  
https://github.com/boostorg/spirit/blob/32537028f5aad270db4df86e4e82b5ffa7d42027/include/boost/spirit/home/x3/auxiliary/attr.hpp#L47  
https://github.com/boostorg/spirit/blob/32537028f5aad270db4df86e4e82b5ffa7d42027/include/boost/spirit/home/x3/auxiliary/attr.hpp#L83

---

## Comment 1

> Username: djowel  
> Created at: 2019-02-27 22:37:39 UTC  
> Url: https://github.com/boostorg/spirit/issues/473#issuecomment-468059209  

I'm not sure if I am following here. Do you have some context and maybe an example?

---

## Comment 2

> Username: Xeverous  
> Created at: 2019-02-27 22:58:37 UTC  
> Url: https://github.com/boostorg/spirit/issues/473#issuecomment-468065121  

```cpp  
    auto const rule_def = (string | '_' >> x3::attr(std::string("default"))) % ','  
```  
text:  
  
```  
"abc", _, "def", _, "ghi"  
```  
  
First occurence of `_`: places `"default"` into the attribute  
  
Second occurence of `_`: places ??? becase string object supplied to `attr` was moved-from

---

## Comment 3

> Username: Kojoley  
> Created at: 2019-02-27 23:13:23 UTC  
> Url: https://github.com/boostorg/spirit/issues/473#issuecomment-468068905  

I overlooked that `std::move` will return `const&&`, so copying will be performed instead.  
  
@Xeverous do you really observe this? This should not happen at least because the string is short for small string optimization to be used and as a result there is nothing to move out from such string.

---

## Comment 4

> Username: djowel  
> Created at: 2019-02-27 23:31:35 UTC  
> Url: https://github.com/boostorg/spirit/issues/473#issuecomment-468073432  

Perhaps the semantics of 'move_to' has changed since then, and the comment:   
// $$$ Change to copy_to once we have it $$$  
is telling. So yes, this should be copy, not move, technically.

---

## Comment 5

> Username: Xeverous  
> Created at: 2019-02-28 11:30:58 UTC  
> Url: https://github.com/boostorg/spirit/issues/473#issuecomment-468239584  

@Kojoley No, I just gaved this as an example.
