# #2517 - Misplaced static_assert in basic_field's move-assignment operator [Closed]

> Username: Tradias  
> Created at: 2022-09-08 14:34:09 UTC  
> Updated at: 2024-06-09 14:53:13 UTC  
> Closed at: 2024-06-09 14:53:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2517  

### Version of Beast: 1.80.0  
  
### Steps necessary to reproduce the problem  
  
https://godbolt.org/z/vdfYrEE1e  
  
Error:  
  
```  
/opt/compiler-explorer/libs/boost_1_80_0/boost/beast/http/impl/fields.hpp: In instantiation of 'boost::beast::http::basic_fields<Allocator>& boost::beast::http::basic_fields<Allocator>::operator=(boost::beast::http::basic_fields<Allocator>&&) [with Allocator = std::pmr::polymorphic_allocator<>]':  
<source>:7:31:   required from here  
/opt/compiler-explorer/libs/boost_1_80_0/boost/beast/http/impl/fields.hpp:440:58: error: static assertion failed: Allocator must be noexcept assignable.  
  440 |     static_assert(is_nothrow_move_assignable<Allocator>::value,  
      |                                                          ^~~~~  
/opt/compiler-explorer/libs/boost_1_80_0/boost/beast/http/impl/fields.hpp:440:58: note: 'boost::integral_constant<bool, false>::value' evaluates to false  
Compiler returned: 1  
```  
  
### Proposed solution  
  
Move the static_assertion from [fields.hpp#L440](https://github.com/boostorg/beast/blob/17141a331ad4943e76933e4db51ef48a170aaf84/include/boost/beast/http/impl/fields.hpp#L440) to the noexcept specifier:  
  
```cpp  
template<class Allocator>  
auto  
basic_fields<Allocator>::  
operator=(basic_fields&& other) noexcept(  
    std::conjunction_v<alloc_traits::propagate_on_container_move_assignment, is_nothrow_move_assignable<Allocator>>)  
      -> basic_fields&  
{  
```  
  
And actually move assign the allocator instead of copy assigning it in [fields.hpp#L1132](https://github.com/boostorg/beast/blob/17141a331ad4943e76933e4db51ef48a170aaf84/include/boost/beast/http/impl/fields.hpp#L1132).

---

## Comment 1

> Username: madmongo1  
> Created at: 2022-09-08 17:40:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2517#issuecomment-1241031551  

The same is true for std::vector.  
https://godbolt.org/z/3rxj9sKs7  
  
@vinniefalco What (if any) is the intention here?

---

## Comment 2

> Username: Tradias  
> Created at: 2022-09-08 17:46:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2517#issuecomment-1241038192  

@madmongo1 your example is malformed, here is the corrected one that compiles successfully: https://godbolt.org/z/aaKjbaoEc

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-09-08 23:06:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2517#issuecomment-1241321818  

> @vinniefalco What (if any) is the intention here?  
  
I have no idea to be honest. Allocators are cancer.

---

## Comment 4

> Username: xbreak  
> Created at: 2022-09-23 09:01:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2517#issuecomment-1255962261  

I ran into this as well and found related background in [p0337 - Delete operator= for polymorphic_allocator](https://wg21.link/p0337)  
  
> Given that a polymorphic_allocator is not propagated on assignment, one may question the usefulness of the assignment operators. Indeed, it seems that most uses of assignment for polymorphic_allocator are likely to be errors.

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2022-09-24 04:10:56 UTC  
> Url: https://github.com/boostorg/beast/issues/2517#issuecomment-1256850967  

The static assert doesn't seem to be misplaced, move-assignments work fine: https://godbolt.org/z/7Pnn99KzW  
  
What is needed is support for `propagate_on_container_move_assignment`.

---

## Comment 6

> Username: Tradias  
> Created at: 2022-09-24 07:15:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2517#issuecomment-1256890801  

@klemens-morgenstern ? Your example doesn't perform move-assignment at all: https://godbolt.org/z/vdfYrEE1e

---

## Comment 7

> Username: Tradias  
> Created at: 2022-09-24 07:26:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2517#issuecomment-1256893004  

Or maybe a more simplified example showing it is unrelated to `propagate_on_container_move_assignment`: https://godbolt.org/z/Kb8d66fb5
