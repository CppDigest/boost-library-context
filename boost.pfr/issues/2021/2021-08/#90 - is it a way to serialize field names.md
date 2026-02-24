# #90 - is it a way to serialize field names? [Closed]

> Username: denzor200  
> Created at: 2021-08-08 00:48:21 UTC  
> Updated at: 2024-02-14 17:58:50 UTC  
> Closed at: 2024-02-14 17:58:50 UTC  
> Url: https://github.com/boostorg/pfr/issues/90  

```  
#ifdef _STR  
#  error _STR already defined  
#endif  
#define _STR(S) BOOST_METAPARSE_STRING(S){}  
  
struct gps_position  
{  
    field<int, (name=_STR("degrees"))> degrees;  
    field<int, (name=_STR("minutes"))> minutes;  
    field<float, (name=_STR("seconds"))> seconds;  
};  
```  
We need to:  
  
1. `field<T, A>` template class, this class is just a wrapper to T with `std::reference_wrapper`-like(??) iface.  
2. `name` object with overloaded `operator=(std::string_view)`, and this overloaded operator must be constexpr  
  
This short example:  
https://godbolt.org/z/nT4bdrnKc  
Interface of `field_t<T,A>` class is not ideal, but this short example shows that structure with named fields are possible.

---

## Comment 1

> Username: denzor200  
> Created at: 2021-08-08 00:56:25 UTC  
> Url: https://github.com/boostorg/pfr/issues/90#issuecomment-894724900  

Also, this approach allows us to to make field with N names.  
For example, each field has:  
  
- default name  
- boost.serialization's name  
- Some orm's name  
  
```  
struct gps_position  
{  
    field<int, (name=_STR("degrees")), (serialization_name=_STR("sdegrees")), (orm_name=_STR("odegrees"))> degrees;  
    field<int, (name=_STR("minutes")), (serialization_name=_STR("sminutes")), (orm_name=_STR("ominutes"))> minutes;  
    field<float, (name=_STR("seconds")), (serialization_name=_STR("sseconds")), (orm_name=_STR("oseconds"))> seconds;  
};  
```  
  
Also we can bind a C++ keyword as field name:  
```  
struct A  
{  
   field<std::string, (name=_STR("new"))> _new;  
};  
```

---

## Comment 2

> Username: denzor200  
> Created at: 2021-08-08 01:06:55 UTC  
> Url: https://github.com/boostorg/pfr/issues/90#issuecomment-894725796  

Continuing the ORM topic.. As a `field_t` parameter, we can pass any attribute, not just a name:  
```  
struct User {  
    field<int,                             name=_STR("id"), autoincrement, primary_key>          id;  
    field<std::string,                     name=_STR("login"), unique>                           login;  
    field<int,                             name=_STR("birth_date")>                              birthDate;  
    field<std::unique_ptr<std::string>,    name=_STR("image_url")>                               imageUrl;  
    field<int,                             name=_STR("type_id")>                                 typeId;  
};  
```

---

## Comment 3

> Username: X-Ryl669  
> Created at: 2022-03-25 12:32:00 UTC  
> Url: https://github.com/boostorg/pfr/issues/90#issuecomment-1078985345  

How is is better than a macro then ?  
```cpp  
struct User {  
  F(int, id);  
  F(std::string, login);  
};  
```

---

## Comment 4

> Username: schaumb  
> Created at: 2023-06-10 17:47:54 UTC  
> Updated at: 2023-06-19 13:40:31 UTC  
> Url: https://github.com/boostorg/pfr/issues/90#issuecomment-1585751700  

I wrote a demo where gcc and msvc can "extract" the members name without any macro [1], from C++20 standard based on pfr.  
With this technique clang will not work (only for constexpr default initializable classes)[2]**.  
  
  
  
[1] https://godbolt.org/z/4nGGh6a4f  
```  
struct Any {  
    Any() {};  
};  
  
struct XClass {  
    int member1;  
    Any this_is_a_name; // not constexpr constructible  
    std::reference_wrapper<char> c; // not default constructible  
};  
  
int main() {  
    static_assert("member1"        == name<0, XClass>());  
    static_assert("this_is_a_name" == name<1, XClass>());  
    static_assert("c"              == name<2, XClass>());  
}  
```  
  
---  
  
[2] clang is not fully C++20 compliant.  
![kép](https://github.com/boostorg/pfr/assets/6457941/a4e7eca2-2a1d-453d-bfa6-515bd26485f0)  
  
---  
  
** but there is a method where "constexpr literal types" works on clang too. See my newer comments.

---

## Comment 5

> Username: denzor200  
> Created at: 2023-06-13 12:08:49 UTC  
> Url: https://github.com/boostorg/pfr/issues/90#issuecomment-1589177307  

@schaumb Fantastic! Just two questions:  
  
-Isn't there any UB in manipulating pointers of union non active member?  
-Using this way will lead to creating a lot of objects with static storage duration. Wouldn't it be worsen for binaryes size?  
  
@apolukhin what do you think about it?

---

## Comment 6

> Username: schaumb  
> Created at: 2023-06-13 13:42:30 UTC  
> Url: https://github.com/boostorg/pfr/issues/90#issuecomment-1589347089  

@denzor200   
  
- UB: It's all made in compile time, so if there is an UB, the compiler will not compile (or it will crash). The union not active member pointers are not UB as far as I know (at least until its value not read or write)  
- I cannot see any object in static storage, only the names: https://godbolt.org/z/xz9n446vG

---

## Comment 7

> Username: X-Ryl669  
> Created at: 2023-06-13 14:25:16 UTC  
> Url: https://github.com/boostorg/pfr/issues/90#issuecomment-1589423602  

Impressive!  
  
Just a question, this seems to rely on compiler storing the complete type of the union field in `__FUNCSIG__` or `__PRETTY_FUNC__`. Those are not standard, right ?  
  
 I don't understand how the union's f member captured from the destructured binding of T into a std::tuple still reference the member's name and not its type. Can you explain this part ?

---

## Comment 8

> Username: schaumb  
> Created at: 2023-06-13 15:20:39 UTC  
> Updated at: 2023-06-13 15:21:56 UTC  
> Url: https://github.com/boostorg/pfr/issues/90#issuecomment-1589530490  

@X-Ryl669   
  
- It's rely on not standard macro like you wrote. But it is widely supported:  
  - GCC, Clang, MSVC, Apple Clang, EDG eccp, Intel C++, (and other gcc variants; and probably I missed some)  
  
(note: As a fallback RUNTIME library, it can be use `typeid(template_struct<member_pointer>).name()` which stores the info (at least gcc; MSVC not); probably a demangle function to this name is necessary).  
  
- The only reference here is at structured binding but the variables weren't read or wrote, only accessed the memory addresses (pointers). Those pointers are copied to the tuple.   
  - The tuple not stores type, but constexpr static variable's member addresses. When the `<auto* p>` templated function requests the `__PRETTY_FUNCTION__` with the `p` value any of member variable address, the text result be like `p = (& union_val.f.member1)`. So we don't query the type of member, but the constexpr variable member location.

---

## Comment 9

> Username: schaumb  
> Created at: 2023-06-19 14:42:29 UTC  
> Url: https://github.com/boostorg/pfr/issues/90#issuecomment-1597313072  

I tried to make it work on clang too, and I succeeded:  
https://godbolt.org/z/e3ba6Pd7q  
Only one caveat, clang only accepts literal types (constexpr initializable types).  
  
If someone creates a lib (or PR) from this "hack", please mention me/my github acc.

---

## Comment 10

> Username: denzor200  
> Created at: 2023-06-20 14:54:01 UTC  
> Url: https://github.com/boostorg/pfr/issues/90#issuecomment-1598951846  

@schaumb I think I will create the PR having finished the traveling. I don't know when will it be, maybe in a month. No problem about copyrights, you will be mentioned :)  
And thank you for discovering a great way to reach the feature that we were waiting for.

---

## Comment 11

> Username: falemagn  
> Created at: 2023-06-21 10:20:04 UTC  
> Url: https://github.com/boostorg/pfr/issues/90#issuecomment-1600577283  

Il giorno mar 13 giu 2023 alle ore 15:42 Bela Schaum  
***@***.***> ha scritto:  
>  
> @denzor200  
>  
> UB: It's all made in compile time, so if there is an UB, the compiler will not compile (or it will crash). The union not active member pointers are not UB as far as I know (at least until its value not read or write)  
  
"Undefined" means that it's not established what happens. It might  
crash, it might produce wrong code, or it might also just work, what  
happens it's not given. Also, correct me if I am wrong, you are  
actually accessing the non-active member, via this line:  
  
    auto& [m1, m2, m3] = *ptr;

---

## Comment 12

> Username: X-Ryl669  
> Created at: 2023-06-21 10:55:43 UTC  
> Updated at: 2023-06-21 11:07:50 UTC  
> Url: https://github.com/boostorg/pfr/issues/90#issuecomment-1600622503  

The code is doing this:  
```cpp   
template<class T>  
constexpr auto members() {  
    // TODO for all 1..N  
    auto& [m1, m2, m3] = union_val<T>.f;  
    return std::tuple{&m1, &m2, &m3};  
}  
```  
where `union_val<T>.f` is a `XClass` or the structure you want to analyze.  
That's instantiated as:  
```cpp  
template<>  
union union_type<XClass>  
{  
  inline constexpr ~union_type() noexcept  
  {  
  }  
  char c = {};  
  XClass f;  
};  
  
template<>  
inline constexpr const union_type<XClass> union_val<XClass> = {{}};  
```  
  
It's taking a reference on each member of the structure `XClass`, not the union, and from this reference, it computes their addresses when constructing the `std::tuple`. I'm not sure if the default construction of the union makes a `c` or a `f` but it's something that quite simple to fix (exchange the order of the field in the union declaration), except that it requires `XClass` to be default constructible (which should be the case for simple aggregate structures but not in the given example).  
  
It works well also with:  
```cpp  
template <typename T>  
union union_type {  
  constexpr ~union_type() {}  
  T f = {};  
  char c;  
};  
```  
but you'll need to make `f` default constructible.   
  
  
If I understand correctly, the destructured binding here is perfectly fine, and it's used in PFR also to detect the field type and position/count.  
  
IMHO, the only UB is the __PRETTY_FUNC__ and other special macro that aren't standard compliant. But since there is no standard compliant way to capture a type and convert it to its textual representation, I guess that a "compiler driver" code that's worth using.

---

## Comment 13

> Username: schaumb  
> Created at: 2023-06-21 12:13:57 UTC  
> Url: https://github.com/boostorg/pfr/issues/90#issuecomment-1600723937  

[Note] The `members` function can be made `consteval`, so it will no effect on runtime. -> No UB on runtime

---

## Comment 14

> Username: schaumb  
> Created at: 2023-06-21 13:54:44 UTC  
> Url: https://github.com/boostorg/pfr/issues/90#issuecomment-1600882388  

[Note2] @X-Ryl669 The whole union wrapper thing made because not just constexpr initializable structures members can be reflected.  
union_type is unnecessary if we don't use it for this reason.

---

## Comment 15

> Username: X-Ryl669  
> Created at: 2023-06-21 20:36:49 UTC  
> Updated at: 2023-06-21 20:44:03 UTC  
> Url: https://github.com/boostorg/pfr/issues/90#issuecomment-1601636276  

Yes, I understood this. If @falemagn wanted no UB while using the union, it could have been specialized by the "is_default_constructible" trait (as an additional template parameter that's partially specialized), so in that case, no access to the non-constructed member would be done.   
  
But, that way, as you said, you can't reflect non-default-constructible structure. Another option would be to declare a template `const T & make_reflector<T>()` that's never called and store a `std::reference_wrapper` in the union so you can extract the type that's never constructed, a bit like (not tested):   
```cpp  
template <typename T>  
union union_type {  
constexpr ~union_type() {}  
  std::reference_wrapper<T> f;  
  char c;  
};  
  
template <typename T>  
constexpr T & make_reflector<T>();  
  
  
template<>  
inline constexpr const union_type<XClass> union_val<XClass> = {.f = make_reflector<T>{}};  
  
template<class T>  
constexpr auto members() {  
    // TODO for all 1..N  
    auto& [m1, m2, m3] = union_val<T>.f;  
    return std::tuple{&m1, &m2, &m3};  
}  
```  
or even simpler:  
```cpp  
template <typename T>  
constexpr inline std::reference_wrapper<T> ref_val{make_reflector<T>()};  
  
template<class T>  
constexpr auto members(T & f) {  
    // TODO for all 1..N  
    auto& [m1, m2, m3] = f;  
    return std::tuple{&m1, &m2, &m3};  
}  
  
// To be called as:  
members(ref_val<T>)  
```  
Or something in the same vein. As you said, since the code is never called at runtime, I doubt it would be an issue with UB here, even the way you've written it (and a compiler would warn you if it was). I don't know all the tricks that can be used to create a "virtual" instance of a structure without actually creating any real instance, yet, I was never bit by this in SFINAE code, so I would assume it's pretty safe.

---

## Comment 16

> Username: schaumb  
> Created at: 2023-06-22 20:34:06 UTC  
> Updated at: 2023-06-22 20:59:47 UTC  
> Url: https://github.com/boostorg/pfr/issues/90#issuecomment-1603280692  

Thanks @X-Ryl669 !  
This helped me to create an union and ub free solution, and without requiring constexpr initializing:  
  
```cpp  
  
template <typename T>  
extern T obj;  
  
template<class T>  
consteval auto members() {  
    // TODO for all 1..N  
    auto& [m1, m2, m3] = obj<T>;  
    return std::tuple{&m1, &m2, &m3};  
}  
```  
  
Clang prints warning (undefined-var-template), but that can be ignore  
  
https://godbolt.org/z/oE7o1Mjx9  
  
or other version if name() returns with a `const char*` : https://godbolt.org/z/WxPqrWE63

---

## Comment 17

> Username: denzor200  
> Created at: 2023-09-10 03:08:05 UTC  
> Url: https://github.com/boostorg/pfr/issues/90#issuecomment-1712697050  

Hello everyone! I've posted for review PR based on the "hack" discussed before: https://github.com/boostorg/pfr/pull/129  
Any feedback would be great. Thanks!

---

## Comment 18

> Username: schaumb  
> Created at: 2023-11-22 18:22:53 UTC  
> Url: https://github.com/boostorg/pfr/issues/90#issuecomment-1823264890  

This issue can be closed because the `get_name` functionality is merged.
