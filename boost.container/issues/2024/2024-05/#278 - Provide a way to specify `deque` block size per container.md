# #278 - Provide a way to specify `deque` block size per container [Closed]

> Username: Lastique  
> Created at: 2024-05-21 07:38:31 UTC  
> Updated at: 2024-05-21 12:50:42 UTC  
> Closed at: 2024-05-21 11:16:01 UTC  
> Url: https://github.com/boostorg/container/issues/278  

`deque_block_size` allows to customize the allocation block size for a given value type. But this affects any `deque` with this value type, and this may not be desired.  
  
It would be useful to have a template parameter in the `deque` template to be able to specify the block size (in the number of container elements) to be used for that container.

---

## Comment 1

> Username: Lastique  
> Created at: 2024-05-21 11:16:01 UTC  
> Url: https://github.com/boostorg/container/issues/278#issuecomment-2122398904  

Turns out, there already is an option for `deque` to specify block size.  
  
https://www.boost.org/doc/libs/master/doc/html/container/configurable_containers.html#container.configurable_containers.configurable_deques

---

## Comment 2

> Username: igaztanaga  
> Created at: 2024-05-21 12:50:41 UTC  
> Url: https://github.com/boostorg/container/issues/278#issuecomment-2122565361  

The option is already there, it's called "block_size", but it wasn't properly documented in the reference. Fixed here:  
  
https://github.com/boostorg/container/commit/8d179787482f389d9dc564273e81b651b4a4f088
