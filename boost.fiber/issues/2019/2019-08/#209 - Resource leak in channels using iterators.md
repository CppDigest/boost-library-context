# #209 - Resource leak in channels using iterators [Closed]

> Username: zpyatt  
> Created at: 2019-08-20 04:42:33 UTC  
> Updated at: 2019-08-20 07:54:00 UTC  
> Closed at: 2019-08-20 07:54:00 UTC  
> Url: https://github.com/boostorg/fiber/issues/209  

First thanks for the awesome library. Second please forgive my ignorance w.r.t properly raising an issue; technically, I think I should do a pull request or something.  
  
I'm using **buffered_channel** with a variant type and one of the variants contains a vector. I noticed memory use just kept increasing on my system monitor, so I tried valgrind and memory sanitizers and didn't find any leaks (which is kind of surprising given my conclusions). Finally, I narrowed the problem down to the area of my code using the variant with the vector. I then made my own vector that logged creation, copy, move, assignment, and destruction with addresses. I noticed one of the move addresses was never getting free'd, which I narrowed down to the use of my range-based-for loop on the channel. Out of curiosity I replaced the range-for with a regular **pop** and the issue went away. So then I investigated the iterator code and believe the problem is in lines 541 - 548 of buffered_channel.hpp:  
  
```cpp  
void increment_() {  
  BOOST_ASSERT( nullptr != chan_);  
  try {  
    ::new ( static_cast< void * >(std::addressof(storage_))) value_type{ chan_->value_pop() };  
  } catch ( fiber_error const&) {  
    chan_ = nullptr;  
  }  
}   
```  
  
It seems to me like any object put in placement new is just going to get overwritten, not destroyed per say. This is especially problematic if the object contains heap allocated resources like vectors or strings. Out of curiosity I added:  
  
```cpp  
reinterpret_cast<value_type*>(std::addressof(storage_) )->~value_type();  
```  
  
before the placement new (I've seen this in a lot of variant implementations that use this pattern), this worked erratically and I realized sometimes `storage_` isn't going to hold a value, particularly on the 1st call to increment. So I moved the destructor to `opertor++` just before the call to increment:  
```cpp  
iterator & operator++() {  
  reinterpret_cast<value_type*>(std::addressof(storage_) )->~value_type();  
  increment_();  
  return * this;  
}  
```  
This way the object only gets destroyed after an object has been created (in the constructor when increment gets called). The memory leak went away and the code seems to work fine. I'm just not sure this is the correct/best way to address the problem, but I'm pretty sure this is a defect (**unbuffered_channel** is the same). Hopefully, I know what I'm talking about and not wasting everyone's time. Thanks.

---

## Comment 1

> Username: olk  
> Created at: 2019-08-20 07:54:00 UTC  
> Url: https://github.com/boostorg/fiber/issues/209#issuecomment-522899473  

fixed - ty
