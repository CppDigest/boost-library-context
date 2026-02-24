# #42 - The names from_bulk and to_bulk are too generic for ADL customization points [Closed]

> Username: mzimbres  
> Created at: 2023-01-17 18:42:11 UTC  
> Updated at: 2023-02-02 07:35:22 UTC  
> Closed at: 2023-02-02 07:33:45 UTC  
> Url: https://github.com/boostorg/redis/issues/42  

See this for more context https://lists.boost.org/Archives/boost/2023/01/253880.php.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2023-01-17 19:01:09 UTC  
> Url: https://github.com/boostorg/redis/issues/42#issuecomment-1385909185  

If this is a customization point why not use `tag_invoke`?

---

## Comment 2

> Username: mzimbres  
> Created at: 2023-02-02 07:35:21 UTC  
> Url: https://github.com/boostorg/redis/issues/42#issuecomment-1413275045  

Replacing ADL with tag_invoke is to take place here: https://github.com/boostorg/redis/issues/58
