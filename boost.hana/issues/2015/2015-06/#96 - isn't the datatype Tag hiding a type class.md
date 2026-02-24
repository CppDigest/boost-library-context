# #96 - [Question] isn't the datatype Tag hiding a type class? [Closed]

> Username: viboes  
> Created at: 2015-06-13 13:18:56 UTC  
> Updated at: 2015-06-14 14:49:28 UTC  
> Closed at: 2015-06-14 14:49:28 UTC  
> Url: https://github.com/boostorg/hana/issues/96  

As several types can share the same data_type, as it is the case for a Map, isn't this hiding an AssociativeMap  type class?

---

## Comment 1

> Username: ldionne  
> Created at: 2015-06-13 17:37:14 UTC  
> Url: https://github.com/boostorg/hana/issues/96#issuecomment-111735896  

I'm not sure I understand your question, could you please be more specific? In particular, I don't understand this part:  
  
> several types can share the same data_type, as it is the case for a Map  
  
It is correct that several types can share the same `datatype`. For example `decltype(int_<0>)` and `decltype(int_<1>)` are different types, but they both share the `IntegralConstant<int>` tag. But I fail to see the link with `Map`.

---

## Comment 2

> Username: viboes  
> Created at: 2015-06-14 12:29:44 UTC  
> Url: https://github.com/boostorg/hana/issues/96#issuecomment-111822212  

E.g. `tuple<pair<x,y>>`,  `tuple<tuple<x,y>>` and `my_named_tuple<x(y)>` all of them could be seen as an AssociativeMap, as the elements can be seen as a `Product` and the type constructor can be seen as a `Sequence` . Am I missing something?

---

## Comment 3

> Username: ldionne  
> Created at: 2015-06-14 12:34:36 UTC  
> Url: https://github.com/boostorg/hana/issues/96#issuecomment-111822433  

`Tuple` does not enforce the uniqueness of elements, whereas `Map` does. Also, the order of elements in a `Map` is not important to determine the equality of `Map`s, whereas it makes a difference for `Tuple`. They are really just different structures designed for different use cases.

---

## Comment 4

> Username: viboes  
> Created at: 2015-06-14 14:28:46 UTC  
> Url: https://github.com/boostorg/hana/issues/96#issuecomment-111832203  

ok. tuple is not correct for a map, but `my_named_tuple` could be.

---

## Comment 5

> Username: ldionne  
> Created at: 2015-06-14 14:30:45 UTC  
> Url: https://github.com/boostorg/hana/issues/96#issuecomment-111833568  

IIUC, your comment is about the fact that we're missing an `AssociativeSequence` concept, right? If so, that's one of the goals of my GSoC to define a proper concept for associative sequences.

---

## Comment 6

> Username: viboes  
> Created at: 2015-06-14 14:46:01 UTC  
> Url: https://github.com/boostorg/hana/issues/96#issuecomment-111836355  

Perfect :)

---

## Comment 7

> Username: ldionne  
> Created at: 2015-06-14 14:49:28 UTC  
> Url: https://github.com/boostorg/hana/issues/96#issuecomment-111837375  

Superb. Closing this in favour of #111.
