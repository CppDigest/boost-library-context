# #763 - object operator[] and insert functions invalidate iterators/references [Closed]

> Username: gummif  
> Created at: 2022-10-10 21:23:04 UTC  
> Updated at: 2023-06-14 10:12:32 UTC  
> Closed at: 2023-06-14 10:12:32 UTC  
> Url: https://github.com/boostorg/json/issues/763  

I was implementing stable_insert and my tests kept failing with all kinds of errors. Well, turns out operator[] can invalidate pointers (on clang this happens, not gcc) and probably some other functions also, even if no insertion takes place.  
  
The spec says  
>  If an insertion occurs and results in a rehashing of  
        the container, all iterators are invalidated. Otherwise  
        iterators are not affected. References are not  
        invalidated. Rehashing occurs only if the new  
        number of elements is greater than @ref capacity().  
  
This happens because emplace/insert reserve memory before checking if the object exists, so reallocation can occur even if it exists.  
  
### Version of Boost  
  
1.80 (develop)  
  
### Steps necessary to reproduce the problem  
  
```c++  
object o = {  
    { "k1", 1 },  
    { "k2", 2 },  
    { "k3", 3 } };  
fprintf(stderr, "begin %p begin + 1 %p - size %d cap %d\n", o.begin(), o.begin() + 1, (int)o.size(), (int)o.capacity());  
auto& v = o["k2"];  
fprintf(stderr, "begin %p begin + 1 %p - size %d cap %d\n", o.begin(), o.begin() + 1, (int)o.size(), (int)o.capacity());  
```  
example output  
```  
begin 0x60d000024af0 begin + 1 0x60d000024b18 - size 3 cap 3  
begin 0x60f0000041f0 begin + 1 0x60f000004218 - size 3 cap 4  
```  
  
### All relevant compiler information  
```  
$ clang --version  
clang version 11.0.0  
Target: x86_64-pc-linux-gnu  
```

---

## Comment 1

> Username: grisumbras  
> Created at: 2022-10-10 21:50:27 UTC  
> Url: https://github.com/boostorg/json/issues/763#issuecomment-1273849546  

This is an unfortunate result of the fact that `operator[]` can insert an element and that our implementation is very eager to reserve space for an extra element. We are planning to make more of our insertions functions to provide strong guarantee, which should fix this bug.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2022-10-10 22:50:35 UTC  
> Url: https://github.com/boostorg/json/issues/763#issuecomment-1273887106  

we could refactor `object::iterator` so it stores `object_impl* and std::size_t index` and then it would not be invalidated on a realloc

---

## Comment 3

> Username: grisumbras  
> Created at: 2022-10-10 22:51:47 UTC  
> Url: https://github.com/boostorg/json/issues/763#issuecomment-1273887816  

The issue here is that no realloc should have happened

---

## Comment 4

> Username: gummif  
> Created at: 2022-10-11 15:42:06 UTC  
> Updated at: 2022-10-11 21:02:39 UTC  
> Url: https://github.com/boostorg/json/issues/763#issuecomment-1274904775  

Am I wrong or would it in e.g. emplace not simply be  
```c++  
    //reserve(size() + 1);  
    auto const result = detail::find_in_object(*this, key);  
    if(result.first)  
        return { result.first, false };  
    reserve(size() + 1); // move here  
    key_value_pair kv(key,  
        std::forward<Arg>(arg), sp_);  
    return { insert_impl(pilfer(kv),  
        result.second), true };  
```  
edit:  
Actually also `if (capacity() == 0) reserve(size() + 1);` before find_in_object.
