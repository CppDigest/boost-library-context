# #309 - Docs should declare destructors as `noexcept` [Closed]

> Username: akrzemi1  
> Created at: 2020-09-10 11:53:52 UTC  
> Updated at: 2020-09-16 01:59:48 UTC  
> Closed at: 2020-09-16 01:59:48 UTC  
> Url: https://github.com/boostorg/json/issues/309  

I do not know how it works, but on branch develop I can see `storage_ptr`'s destuctor declard `noexcept`, but this is not reflected in the compiled documentation at http://develop.json.cpp.al/json/ref/boost__json__storage_ptr/_storage_ptr.html  
  
The docs should reflect this `noexcept`, since it makes a difference: one cannot infer from these declarations if destructor is `noexcept` or not, as it depends on class members which are not exposed in the documentation.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-09-12 19:54:17 UTC  
> Url: https://github.com/boostorg/json/issues/309#issuecomment-691537674  

Actually destructors should not be declared `noexcept` unless they are explicitly defaulted.

---

## Comment 2

> Username: akrzemi1  
> Created at: 2020-09-14 06:14:41 UTC  
> Url: https://github.com/boostorg/json/issues/309#issuecomment-691836944  

The reason I insist on this is that without the `noexcept` annotation on destructor, as a user, I am not guaranteed that `std::is_nothrow_move_constructible_v<storage_ptr>` will return `true`. The non-throwing exception specification cannot be determined by a library user only from expecting the destructor's declaration. One also needs o know the definitions of class members, but this is an implementation detail of the library and the user doesn't have access to it.  
  
Here is an example that illustrates this:  
  
```c++  
struct Thrower  
{  
  bool pleaseThrow;  
  explicit Thrower(bool pt) : pleaseThrow(pt) {}  
  Thrower(Thrower&&) noexcept = default;  
  ~Thrower() noexcept(false) { if (pleaseThrow) throw 0; }  
};  
  
class ImplementationDetail  
{  
private:  
  Thrower t {false}; // invariant: t.pleaseThrow == false  
      
public:  
  ImplementationDetail(ImplementationDetail&&) noexcept = default;  
  ~ImplementationDetail() = default /* throws: nothing */;  
};  
  
struct storage_ptr  
{  
  ImplementationDetail impl;  
  // destructor throws: nothing  
};  
  
int main()  
{  
    std::cout << std::is_nothrow_move_constructible_v<storage_ptr>::value;  
}  
```    
  
This outputs `0`, even though move constructor is marked `noexcept` and destructor never throws.   
  
Also, note how the Standard Library handles this [[res.on.exception.handling] para3](http://eel.is/c++draft/requirements#res.on.exception.handling-3):   
  
> Destructor operations defined in the C++ standard library shall not throw exceptions. *Every destructor in the C++ standard library shall behave as if it had a non-throwing exception specification.*  
  
Given that, I consider claim "destructors should not be declared `noexcept` unless they are explicitly defaulted" incorrect: this gives sufficient guarantee to the library author, who sees class member declarations, but not to the library user.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-09-14 07:22:20 UTC  
> Url: https://github.com/boostorg/json/issues/309#issuecomment-691870870  

@pdimov ?

---

## Comment 4

> Username: pdimov  
> Created at: 2020-09-14 11:09:12 UTC  
> Updated at: 2020-09-14 11:10:57 UTC  
> Url: https://github.com/boostorg/json/issues/309#issuecomment-691983950  

There's no harm in declaring destructors `noexcept` _in the docs_, but the use of Doxygen may not make this trivial to achieve. The standard library doesn't do this though. `noexcept` is implied on a destructor and is not spelled out.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2020-09-15 16:12:34 UTC  
> Url: https://github.com/boostorg/json/issues/309#issuecomment-692819602  

@akrzemi1 would declaring the destructors noexcept in the docs only be sufficient for your needs?

---

## Comment 6

> Username: akrzemi1  
> Created at: 2020-09-15 16:16:01 UTC  
> Url: https://github.com/boostorg/json/issues/309#issuecomment-692821571  

Yes. That would be a commitment from your site that `storage_ptr` will be detected as noexcept-move-constructible.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2020-09-15 16:18:21 UTC  
> Url: https://github.com/boostorg/json/issues/309#issuecomment-692823003  

So I should `static_assert( std::is_nothrow_move_constructible_v<storage_ptr>::value )` ?

---

## Comment 8

> Username: akrzemi1  
> Created at: 2020-09-15 22:14:20 UTC  
> Url: https://github.com/boostorg/json/issues/309#issuecomment-693007813  

That would be a good unit-test.
