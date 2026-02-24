# #153 - Add an attribute-transformation directive [Closed]

> Username: tzlaine  
> Created at: 2024-03-02 02:52:54 UTC  
> Updated at: 2024-03-15 21:29:34 UTC  
> Closed at: 2024-03-15 21:29:34 UTC  
> Url: https://github.com/boostorg/parser/issues/153  

From David Sankel and me, from the Boost review:  
  
> If a parser p has attribute type T (a T parser), and you have a function f of type U(T), I would expect there to be an easy way to convert the T parser into a U parser. Something like map(f, p).  
  
This seems like a nice addition. Though you can certainly do this with a rule+semantic action combo, it would be much more ergonomic to be able to do this in a simpler way.  I would make this a directive, like `transform(f)[p]`.

---

## Comment 1

> Username: akrzemi1  
> Created at: 2024-03-02 12:22:48 UTC  
> Url: https://github.com/boostorg/parser/issues/153#issuecomment-1974783017  

Will this address the use case from https://github.com/tzlaine/parser/issues/63?

---

## Comment 2

> Username: tzlaine  
> Created at: 2024-03-04 03:57:44 UTC  
> Url: https://github.com/boostorg/parser/issues/153#issuecomment-1975654041  

I don't think so, or at least I don't yet see it.  The thing proposed here is taking an attribute that definitely exists, and transforming it into an attribute of a different type.

---

## Comment 3

> Username: akrzemi1  
> Created at: 2024-03-04 17:39:07 UTC  
> Url: https://github.com/boostorg/parser/issues/153#issuecomment-1977124512  

Well, not strictly directive `defaulted[]`, but I can do:  
  
```c++  
transform(map_nullopt_to_default_constructed)[ (char_ >> int_) % ',') | eps ]  
```

---

## Comment 4

> Username: tzlaine  
> Created at: 2024-03-04 23:21:29 UTC  
> Url: https://github.com/boostorg/parser/issues/153#issuecomment-1977640216  

I get it now.  Then, yes, I could definitely be used in that case.  I still think writing `attr(x)` is easier.
