# #438 - Add move semantics support [Closed]

> Username: mloskot  
> Created at: 2020-03-04 18:09:37 UTC  
> Updated at: 2022-05-12 12:20:34 UTC  
> Closed at: 2022-05-12 12:14:02 UTC  
> Url: https://github.com/boostorg/gil/issues/438  

Since GIL switched to C++11, it's the right time to consider enabling move semantics for the core types which own resources, especially the `image` class.  
  
Contributions are welcome. Please, see the [CONTRIBUTING.md](https://github.com/boostorg/gil/blob/develop/CONTRIBUTING.md) for guidelines on how to.

---

## Comment 1

> Username: mocabe  
> Created at: 2020-03-05 07:13:08 UTC  
> Updated at: 2020-03-06 12:08:37 UTC  
> Url: https://github.com/boostorg/gil/issues/438#issuecomment-595065092  

Speaking of `gil::image`, move constructor seems relatively straightforward.  
  
Signature of move constructor should be like:  
```cpp  
image(image&& img)  
```  
Although there're some design spaces here:  
* Should we make move constructor `noexcept`?  
  + This requires copy construction of `view_t` is nothrow operation.  
* Should we add templated version of this like copy constructor?  
  + I'm not really sure why there's templated copy constructor, to be honest. Can someone show me use case of it?  
  
```cpp  
image_t a(...);  
image_t b(std::move(a));  
```  
here's list of internal states on move construction I can think of:  
| `a` before move | Move constructed `b` | Moved after state of `a` |  
|:--:|:--:|:--:|  
| `view_t  _view` | `a._view` | `view_t()` |  
| `unsigned char* _memory` | `a._memory` | `nullptr` |  
| `std::size_t  _align_in_bytes` | `a._align_in_bytes` | `0` |  
| `allocator_type _alloc` | `std::move(a._alloc)` | Allocator specific moved-after state |  
| `std::size_t _allocated_bytes` | `a._allocated_bytes` | `0` |

---

## Comment 2

> Username: sdebionne  
> Created at: 2020-03-19 13:22:47 UTC  
> Url: https://github.com/boostorg/gil/issues/438#issuecomment-601174882  

@mocabe Any chance that you are working on this issue? Just to make sure we are not duplicating work...  
  
About the template copy constructor of `image`, same pixel types but different allocators could be a use case, but not very likely.

---

## Comment 3

> Username: mocabe  
> Created at: 2020-03-19 14:08:49 UTC  
> Url: https://github.com/boostorg/gil/issues/438#issuecomment-601199372  

@sdebionne I haven't really worked on this issue extensively yet, and it'll take several weeks before I can put more effort into this over other tasks I have right now. So if you can actively work on this right now, go for it!  
  
> About the template copy constructor of image, same pixel types but different allocators could be a use case, but not very likely.    
  
Yeah, I though of that but current templated copy constructor is actually copying `private` allocator member so it's not really usable like that since there's no `friend` declaration for other `image` classes...  
  
```cpp  
    template <typename P2, bool IP2, typename Alloc2>  
    image(const image<P2,IP2,Alloc2>& img) : _memory(nullptr), _align_in_bytes(img._align_in_bytes), _alloc(img._alloc)  
                                           , _allocated_bytes( img._allocated_bytes ) {  
       allocate_and_copy(img.dimensions(),img._view);  
    }  
```

---

## Comment 4

> Username: mocabe  
> Created at: 2020-03-19 16:10:09 UTC  
> Url: https://github.com/boostorg/gil/issues/438#issuecomment-601268168  

On move constructors, I'm still not sure we can just put `noexcept` on it. When there are some image view implementations which can possible `throw` on copy construction, they just crashes with `noexcept`. We can set conditional `noexcept` with checks like `noexcept(std::is_nothrow_copy_constructible<view_t>::value)` but now we also need to add `noexcept` for common image views for optimal performance.   
  
Move assignments are even more tricky when we think of allocators.    
Some allocators are not move assignable, which means we need to fallback to copy construction on some specific conditions. As far as I looked though the code, supporting allocators property in `gil::image` requires some large rewrite including copy constructors, so this can be ignored this time I guess.

---

## Comment 5

> Username: mloskot  
> Created at: 2020-03-25 09:49:50 UTC  
> Url: https://github.com/boostorg/gil/issues/438#issuecomment-603745083  

@sdebionne & @mocabe FYI, I'm not ignoring this discussion and the move semantics is a big deal for GIL, it is just taking time for me to find moment and think about it myself. Thanks for your help!

---

## Comment 6

> Username: sdebionne  
> Created at: 2020-03-26 11:27:38 UTC  
> Url: https://github.com/boostorg/gil/issues/438#issuecomment-604376964  

@mloskot Thanks for your support!  
  
@mocabe Shall we just remove the `noexcept` for now then and leave the optimization for later? I can add `// TODO` in the code.  I'll try to handle the case with allocators that are not move assignable. Shall we add a new "Support allocators properly in gil::image" issue?

---

## Comment 7

> Username: mloskot  
> Created at: 2020-03-27 00:00:23 UTC  
> Url: https://github.com/boostorg/gil/issues/438#issuecomment-604746551  

@sdebionne & @mocabe I posted my initial review of #457   
  
> Shall we add a new "Support allocators properly in gil::image" issue?  
  
Feel free to open issues/tasks/todos whenever you think it's useful for yourself or others.  
  
Actually, the move assignment operator sample I attached to the review may address some of those issues that you have in mind.

---

## Comment 8

> Username: sdebionne  
> Created at: 2022-05-12 09:44:46 UTC  
> Url: https://github.com/boostorg/gil/issues/438#issuecomment-1124775111  

@mloskot Do you see any other core types that need move semantic or shall we mark this issue as done?

---

## Comment 9

> Username: mloskot  
> Created at: 2022-05-12 11:36:02 UTC  
> Url: https://github.com/boostorg/gil/issues/438#issuecomment-1124883892  

@sdebionne I haven't checked in details for any other obvious types. The iterators and locators could be. I don't think we have any that manage heavy resources.  
If see no obvious types either, then this can be closed as done.

---

## Comment 10

> Username: sdebionne  
> Created at: 2022-05-12 12:14:02 UTC  
> Url: https://github.com/boostorg/gil/issues/438#issuecomment-1124916483  

AFAIK, all iterators and locators are lightweight (as one would expect such objects to be).

---

## Comment 11

> Username: mloskot  
> Created at: 2022-05-12 12:20:04 UTC  
> Updated at: 2022-05-12 12:20:34 UTC  
> Url: https://github.com/boostorg/gil/issues/438#issuecomment-1124922939  

Yes, they are, and all simple values should be moveable, in the standard library they must be. Here is nice summary of the gist https://stackoverflow.com/a/14316175/151641 That's why I mentioned them.
