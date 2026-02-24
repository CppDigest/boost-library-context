# #444 - Consider using the same atomic ops as boost::shared_ptr` does. [Closed]

> Username: maximilianriemensberger  
> Created at: 2020-10-08 11:32:33 UTC  
> Updated at: 2020-10-09 18:55:02 UTC  
> Closed at: 2020-10-09 18:55:02 UTC  
> Url: https://github.com/boostorg/json/issues/444  

https://github.com/boostorg/json/blob/5098730a87148228f65b2dcb40ecec7f15019c09/include/boost/json/storage_ptr.hpp#L108  
  
`boost::json::storage_ptr` uses sequentially consistent atomic ops for the reference count. This is needlessly expensive on most non-x86 platforms, in particular arm and the like. `boost::shared_ptr` apparently implements more efficient reference counting (usually relaxed inc + acquire release dec). Considering that there's one `storage_ptr` per `value`, that's potentially quite a large number of incs and decs for a large json document. I would suggest that `storage_ptr` reference count operations match those of `boost::shared_ptr`.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-10-08 12:07:26 UTC  
> Url: https://github.com/boostorg/json/issues/444#issuecomment-705524333  

If you want to submit a pull request, I'm more than happy to merge it. Or I can do it. To be honest though, I don't know much about atomic operations except how to write `std::atomic<...>` and use it in the simplest fashion.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-10-08 19:37:05 UTC  
> Url: https://github.com/boostorg/json/issues/444#issuecomment-705780862  

we might want `storage()` to return `storage_ptr const&` again

---

## Comment 3

> Username: maximilianriemensberger  
> Created at: 2020-10-08 19:40:39 UTC  
> Url: https://github.com/boostorg/json/issues/444#issuecomment-705782715  

> we might want `storage()` to return `storage_ptr const&` again  
  
That definitely saves some ref count ops.

---

## Comment 4

> Username: maximilianriemensberger  
> Created at: 2020-10-08 19:42:43 UTC  
> Url: https://github.com/boostorg/json/issues/444#issuecomment-705783644  

> If you want to submit a pull request, I'm more than happy to merge it. Or I can do it. To be honest though, I don't know much about atomic operations except how to write `std::atomic<...>` and use it in the simplest fashion.  
  
I can look into it.

---

## Comment 5

> Username: madmongo1  
> Created at: 2020-10-08 19:43:10 UTC  
> Url: https://github.com/boostorg/json/issues/444#issuecomment-705783858  

Getting atomic ops right beyond using sequential consistency is *hard*. Strongly suggest a review by more than one person if this route is taken (which it should be in order to make pool-based operations more competitive).

---

## Comment 6

> Username: vinniefalco  
> Created at: 2020-10-08 19:44:31 UTC  
> Url: https://github.com/boostorg/json/issues/444#issuecomment-705784466  

well, I _think_ just copying ` boost::shared_ptr` is a no-lose proposition

---

## Comment 7

> Username: madmongo1  
> Created at: 2020-10-08 19:52:05 UTC  
> Url: https://github.com/boostorg/json/issues/444#issuecomment-705787940  

@maximilianriemensberger  Looking at the design of storage_ptr, it would seem that `boost::instrusive_ptr` would be a better model.  
  
https://www.boost.org/doc/libs/1_74_0/libs/smart_ptr/doc/html/smart_ptr.html#intrusive_ptr  
  
https://github.com/boostorg/smart_ptr/blob/develop/include/boost/smart_ptr/detail/atomic_count.hpp  
  
@pdimov - your view?

---

## Comment 8

> Username: vinniefalco  
> Created at: 2020-10-08 19:54:16 UTC  
> Url: https://github.com/boostorg/json/issues/444#issuecomment-705789008  

`storage_ptr` is already modeled after `intrusive_ptr`. It manages an object whose reference counts are in the object itself. Or did you mean something else?

---

## Comment 9

> Username: madmongo1  
> Created at: 2020-10-08 20:02:37 UTC  
> Url: https://github.com/boostorg/json/issues/444#issuecomment-705792994  

I meant that the atomic modification of intrusive_ptr reference counts already has careful treatment in the smart_ptr library.  
It's different for each compiler/platform because of differences in intrisics and differences in the natural atomic strength of operations on different processors.  
e.g. Arm is a lot more fussy than x86 (which is basically bomb proof)

---

## Comment 10

> Username: maximilianriemensberger  
> Created at: 2020-10-08 20:24:40 UTC  
> Url: https://github.com/boostorg/json/issues/444#issuecomment-705803656  

I totally agree that there should be many eyes looking any atomic ops things. #446 is what I think is sufficient. It is what libc++ uses for its shared_ptr implementation (just uglyfied and with intrinsics). I have yet to check the other std libs.   
  
boost::smart_ptr does acq_rel incfrement and decrement instead of relaxed increment and acq_rel decrement. That made me scratch my head. But may @pdimov can shed some light onto this issue.  
  
Anyway I think you should definitely only merge changes to the refcount here after careful review and consideration. Stealing from widely used and reviewed refcount implemenetations (stdlib/boost/...) seems like a good idea nevertheless.

---

## Comment 11

> Username: madmongo1  
> Created at: 2020-10-08 21:42:41 UTC  
> Url: https://github.com/boostorg/json/issues/444#issuecomment-705839346  

I concur. The increment can be merely atomic because there is no dependency on the value after the increment.  
On the other hand the decrement can result in the destruction of the object so it must be acquire (to get the previous value) and release (to communicate the new value to other threads).

---

## Comment 12

> Username: pdimov  
> Created at: 2020-10-08 21:43:15 UTC  
> Url: https://github.com/boostorg/json/issues/444#issuecomment-705839570  

>boost::smart_ptr does acq_rel incfrement and decrement instead of relaxed increment and acq_rel decrement.  
  
Does it? Where?

---

## Comment 13

> Username: maximilianriemensberger  
> Created at: 2020-10-09 06:26:11 UTC  
> Url: https://github.com/boostorg/json/issues/444#issuecomment-705995265  

> > boost::smart_ptr does acq_rel incfrement and decrement instead of relaxed increment and acq_rel decrement.  
>   
> Does it? Where?  
  
I think I incorrectly tracked the implementation down to the `atomic_count` headers, which use acq_rel inc and acq_rel dec. Instead I should have looked at sp_counted_base_std_atomic instead. Sorry for the confusion.

---

## Comment 14

> Username: pdimov  
> Created at: 2020-10-09 10:37:03 UTC  
> Url: https://github.com/boostorg/json/issues/444#issuecomment-706106401  

IIRC the atomic_count headers conservatively use acq_rel because you could, technically, use a negative count and swap the meanings of increment and decrement in your code.

---

## Comment 15

> Username: vinniefalco  
> Created at: 2020-10-09 18:55:02 UTC  
> Url: https://github.com/boostorg/json/issues/444#issuecomment-706349574  

This is done, thanks!
