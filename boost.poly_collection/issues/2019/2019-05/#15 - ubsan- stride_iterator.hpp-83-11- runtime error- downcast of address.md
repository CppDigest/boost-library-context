# #15 - ubsan: stride_iterator.hpp:83:11: runtime error: downcast of address [Closed]

> Username: kjetilj  
> Created at: 2019-05-01 19:13:09 UTC  
> Updated at: 2021-02-05 09:38:24 UTC  
> Closed at: 2021-02-05 09:38:24 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/15  

```  
ziv@ziv-dev:~/Dev/temp$ g++ --version  
g++ (Ubuntu 7.3.0-27ubuntu1~18.04) 7.3.0  
Copyright (C) 2017 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
  
ziv@ziv-dev:~/Dev/temp$ cat main.cpp  
#include <boost/poly_collection/base_collection.hpp>  
  
  
struct Base  
{  
	virtual ~Base() = default;  
};  
  
struct D1 : Base {};  
  
  
int main()  
{  
	boost::base_collection<Base> bases;  
	bases.insert(D1());  
	bases.insert(D1());  
	bases.insert(D1());  
  
	for (auto it = bases.begin<D1>(); it != bases.end<D1>(); ++it);  
}  
ziv@ziv-dev:~/Dev/temp$ g++ -I../boost_1_70_0 -pipe -std=c++17 -fsanitize=undefined -fno-sanitize-recover -g  main.cpp && ./a.out  
../boost_1_70_0/boost/poly_collection/detail/stride_iterator.hpp:83:11: runtime error: downcast of address 0x55ae935fae88 which does not point to an object of type 'D1'  
0x55ae935fae88: note: object has invalid vptr  
 ae 55 00 00  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  31 00 00 00 00 00 00 00  00 00 00 00  
              ^~~~~~~~~~~~~~~~~~~~~~~  
              invalid vptr  
    #0 0x55ae9284bce1 in boost::poly_collection::detail::stride_iterator<Base>::operator D1*<D1, (void*)0>() const ../boost_1_70_0/boost/poly_collection/detail/stride_iterator.hpp:83  
    #1 0x55ae9284a274 in boost::poly_collection::detail::local_iterator_impl<boost::poly_collection::common_impl::poly_collection<boost::poly_collection::detail::base_model<Base>, std::allocator<Base> >, D1*>::local_iterator_impl<boost::poly_collection::detail::stride_iterator<Base> >(std::__detail::_Node_const_iterator<std::pair<std::type_info const* const, boost::poly_collection::detail::segment<boost::poly_collection::detail::base_model<Base>, boost::poly_collection::detail::allocator_adaptor<std::allocator<Base> > > >, false, false>, boost::poly_collection::detail::stride_iterator<Base>) ../boost_1_70_0/boost/poly_collection/detail/iterator_impl.hpp:150  
    #2 0x55ae92848f85 in boost::poly_collection::detail::local_iterator_impl<boost::poly_collection::common_impl::poly_collection<boost::poly_collection::detail::base_model<Base>, std::allocator<Base> >, D1*> boost::poly_collection::common_impl::poly_collection<boost::poly_collection::detail::base_model<Base>, std::allocator<Base> >::end<D1, (void*)0>() ../boost_1_70_0/boost/poly_collection/detail/poly_collection.hpp:484  
    #3 0x55ae92846a1f in main /home/ziv/Dev/temp/main.cpp:19  
    #4 0x7f62b00e7b96 in __libc_start_main (/lib/x86_64-linux-gnu/libc.so.6+0x21b96)  
    #5 0x55ae928465e9 in _start (/home/ziv/Dev/temp/a.out+0x195e9)  
  
ziv@ziv-dev:~/Dev/temp$   
  
```

---

## Comment 1

> Username: joaquintides  
> Created at: 2019-05-07 13:54:00 UTC  
> Updated at: 2019-05-07 14:57:36 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/15#issuecomment-490089620  

Thanks for reporting this issue. UBSan is right in marking this as undefined behavior: as it happens, `bases.end<D1>()`, which is basically a `D1 *`, is obtained via `static_cast` from `bases.end(typeid(D1))`, which is basically a `Base *`. Such a cast is valid except when the `Base *` points just past the end of the segment, in which case the clause of [expr.static.cast]/11 that I've marked in bold applies:  
  
_"A prvalue of type “pointer to cv1 B”, where B is a class type, can be converted to a prvalue of type “pointer to cv2 D”, where D is a complete class derived (11.6) from B, if cv2 is the same cv-qualification as, or greater cv-qualification than, cv1. If B is a virtual base class of D or a base class of a virtual base class of D, or if no valid standard conversion from “pointer to D” to “pointer to B” exists (7.3.11), the program is ill-formed. The null pointer value (7.3.11) is converted to the null pointer value of the destination type. **If the prvalue of type “pointer to cv1 B” points to a B that is actually a subobject of an object of type D, the resulting pointer points to the enclosing object of type D. Otherwise, the behavior is undefined.**"_  
  
For all practical purposes, this cast operation is safe (no actual access to the pointed to object is attempted), so, for the moment being, I've just disabled UBSan for the offending bit (5298636215a9dfbab3919eb6a258a5202fe38ab2). My plan is however to submit a paper to the committee so as to attempt to rephrase [expr.static.cast]/11 to allow for past-the-end pointers.

---

## Comment 2

> Username: kjetilj  
> Created at: 2019-05-07 20:16:57 UTC  
> Url: https://github.com/boostorg/poly_collection/issues/15#issuecomment-490239229  

Thanks, I applied the patch and turned ubsan back on, all OK
