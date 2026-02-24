# #241 - `unordered_node_map::emplace` does not fully enforce pointer stability [Closed]

> Username: kehan01  
> Created at: 2024-04-29 06:04:23 UTC  
> Updated at: 2024-06-08 07:18:23 UTC  
> Closed at: 2024-06-08 07:18:22 UTC  
> Url: https://github.com/boostorg/unordered/issues/241  

It seems like `unordered_node_map` performs a move/copy construction during `emplace` if a move/copy constructor is available for the mapped object. This invalidates pointer stability since the address of the object at construction will differ from its address at the end of the `emplace` call.  See https://godbolt.org/z/WdTsboedx for an example  
  
After a brief look, it appears the following is where the check for the existence of a move/copy constructor occurs:  
https://github.com/boostorg/unordered/blob/73582be56331aab35a43344f3737744bf43b9642/include/boost/unordered/detail/foa/table.hpp#L403-L408  
If a copy/move constructor is available, this passes a constructed (key, value) pair to `emplace_impl` directly - otherwise it will pass an `element_type`. In the former case, I believe it'll eventually resolve to [this](https://github.com/boostorg/core/blob/965508d9e187d589070c5cd1853cc035b51ea3cf/include/boost/core/allocator_access.hpp#L495-L499) version of construct, whereas the latter will resolve to [this](https://github.com/boostorg/unordered/blob/73582be56331aab35a43344f3737744bf43b9642/include/boost/unordered/detail/foa/node_map_types.hpp#L61-L65) version. Please do correct me if I am wrong though

---

## Comment 1

> Username: cmazakas  
> Created at: 2024-04-29 20:41:34 UTC  
> Url: https://github.com/boostorg/unordered/issues/241#issuecomment-2083626016  

Hi, thank you for filing an issue.  
  
We use the presence of a move constructor as an optimization technique during emplace.   
  
Would you mind elaborating a bit on what's incorrect about what Unordered is doing here? In general, we assume that if a type can be moved, it's valid/correct to do so.

---

## Comment 2

> Username: kehan01  
> Created at: 2024-04-29 21:30:55 UTC  
> Url: https://github.com/boostorg/unordered/issues/241#issuecomment-2083711677  

Thanks for the response Christian! Under the assumption that "if an object has a copy/move constructor, then it is safe to move/copy", then maybe there's nothing fundamentally incorrect about emplace's behavior.  
  
However, the main concern here would be that that this behavior is inconsistent with that of `unordered_map`, which makes it very easy for bugs to be introduced when switching from `std` containers. One could argue that from an API perspective, if a user expects the container they are using to offer pointer stability, it should not be up to them to explicitly disable move/copy construction in order to obtain this behavior.

---

## Comment 3

> Username: kehan01  
> Created at: 2024-04-29 21:32:44 UTC  
> Updated at: 2024-04-29 22:08:38 UTC  
> Url: https://github.com/boostorg/unordered/issues/241#issuecomment-2083714154  

>We use the presence of a move constructor as an optimization technique during emplace.  
  
I didn't get a chance to look through the implementation too thoroughly, so I was also a little curious as to why/how this is achieved. Would appreciate any insight you might have, and thanks in advance

---

## Comment 4

> Username: pdimov  
> Created at: 2024-04-29 21:40:34 UTC  
> Url: https://github.com/boostorg/unordered/issues/241#issuecomment-2083730138  

Pointer stability ordinarily means that once the element is in the container, its address is stable. (Hence, if you get a pointer to a container element, you can hold onto it even if the container is modified, as long as the element in question isn't erased.)  
  
It doesn't mean that a value won't be moved prior to being inserted into its final position in the container.

---

## Comment 5

> Username: cmazakas  
> Created at: 2024-04-29 22:43:38 UTC  
> Url: https://github.com/boostorg/unordered/issues/241#issuecomment-2083811298  

> > We use the presence of a move constructor as an optimization technique during emplace.  
>   
> I didn't get a chance to look through the implementation too thoroughly, so I was also a little curious as to why/how this is achieved. Would appreciate any insight you might have, and thanks in advance  
  
Sure.  
  
If you examine: https://github.com/boostorg/unordered/blob/73582be56331aab35a43344f3737744bf43b9642/include/boost/unordered/detail/foa/core.hpp#L1225-L1228  
  
you can see that we conditionally return either the `emplace_type` or the `TypePolicy::element_type` (`element_type` stores a pointer to the value type).  
  
In the case of a movable type, we simply return that otherwise we return the allocated node.  
  
From there, we eventually wind up at one of these two overloads: https://github.com/boostorg/unordered/blob/73582be56331aab35a43344f3737744bf43b9642/include/boost/unordered/detail/foa/node_map_types.hpp#L61  
  
and https://github.com/boostorg/unordered/blob/73582be56331aab35a43344f3737744bf43b9642/include/boost/unordered/detail/foa/node_map_types.hpp#L93-L95  
which as you can see here, allocates.  
  
So basically, we stack-construct the type first and then move it into a delayed allocation if the key isn't found. Otherwise, if the key does exist in the container, we've avoided an unnecessary allocation + deallocation pair because we've only used the stack.  
  
In the case of an immovable type, we have no choice but to eagerly allocate because there's nothing else we can do.  
  
I guess my question is, is there something about these steps that's breaking your application?

---

## Comment 6

> Username: joaquintides  
> Created at: 2024-06-08 07:18:23 UTC  
> Url: https://github.com/boostorg/unordered/issues/241#issuecomment-2155851216  

Closing due to lack of further feedback.
