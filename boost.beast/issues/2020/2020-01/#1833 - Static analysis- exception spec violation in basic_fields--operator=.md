# #1833 - Static analysis: exception spec violation in basic_fields::operator= [Closed]

> Username: michael-baumann  
> Created at: 2020-01-31 15:36:02 UTC  
> Updated at: 2020-03-31 20:09:41 UTC  
> Closed at: 2020-03-31 20:09:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1833  

### Version of Beast  
  
boost 1.71.0 / **266**  
  
Our static analysis tool (coverity) reports the following:  
  
> Uncaught exception (UNCAUGHT_EXCEPT)  
> exn_spec_violation: An exception of type std::length_error is thrown but the throw list noexcept doesn't allow it to be thrown.  
  
in:  
  
In: beast/http/impl/fields.hpp:434  
  
```  
basic_fields<Allocator>::  
operator=(basic_fields&& other) noexcept(  
    alloc_traits::propagate_on_container_move_assignment::value)  
      -> basic_fields&  
```  
  
Calling in beast/http/impl/fields.hpp:443  
```  
move_assign(basic_fields& other, std::true_type)  
```  
  
Calling in beast/http/impl/fields.hpp:1122  
```  
clear_all();  
```  
  
Calling in beast/http/impl/fields.hpp:1102  
```  
realloc_string(string_view& dest, string_view s)  
```  
  
Calling in beast/http/impl/fields.hpp:1040  
```  
a.allocate(s.size());  
```  
  
which throws std::length_error in case requested size exceeds max_size()  
  
### All relevant compiler information  
  
Xcode10.2.1 on MacOS, libc++  
  
(see also  
https://github.com/llvm-mirror/libcxx/blob/master/include/memory line 1850)  
  
I am not familiar enough with these internals, so unsure whether its a false positive or some adaption is necessary, please have look.

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-01-31 16:33:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1833#issuecomment-580807856  

Hi @michael-baumann ,  
  
Thanks for bringing this to our attention.  
  
I will investigate with haste.

---

## Comment 2

> Username: stale[bot]  
> Created at: 2020-03-01 16:58:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1833#issuecomment-593118948  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-03-01 17:01:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1833#issuecomment-593119279  

I believe this was fixed in a recent commit?

---

## Comment 4

> Username: madmongo1  
> Created at: 2020-03-01 19:12:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1833#issuecomment-593133431  

So do i

---

## Comment 5

> Username: stale[bot]  
> Created at: 2020-03-31 20:03:37 UTC  
> Url: https://github.com/boostorg/beast/issues/1833#issuecomment-606845454  

This issue has been open for a while with no activity, has it been resolved?
