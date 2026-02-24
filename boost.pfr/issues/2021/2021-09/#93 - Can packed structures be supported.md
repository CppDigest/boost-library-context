# #93 - Can packed structures be supported? [Open]

> Username: pavelkryukov  
> Created at: 2021-09-18 13:55:30 UTC  
> Updated at: 2025-07-11 07:02:56 UTC  
> Url: https://github.com/boostorg/pfr/issues/93  

I noticed that packed structures are not supported, while it is not mentioned in documentation.  
  
For example:  
  
```c++  
struct __attribute__ ((packed)) A  
{  
    int x;  
    int z;  
    bool y;  
};  
```  
  
The error message is following:  
  
```  
static_assert(sizeof(U) == sizeof(S), "====================> Boost.PFR: Member sequence does not indicate correct size for struct type! Maybe the user-provided type is not a SimpleAggregate?");  
              ~~~~~~~~~~^~~~~~~~~~~~  
```  
Link: https://godbolt.org/z/Exo16Mods  
  
However, the class should be an Aggregate, as  
  
> _An aggregate is an array or a class (clause 9) with no user-declared constructors (12.1), no private or protected non-static data members (clause 11), no base classes (clause 10), and no virtual functions (10.3)._  
  
Is there a technical obstacle on adding packed structures?

---

## Comment 1

> Username: u3shit  
> Created at: 2021-09-25 19:28:36 UTC  
> Url: https://github.com/boostorg/pfr/issues/93#issuecomment-927172101  

Please note that `__attribute__((packed))` is a gcc extension, so what does the the c++ standard say about aggregates is irrelevant here. Unfortunately, gcc's documentations is pretty terse on this subject: https://gcc.gnu.org/onlinedocs/gcc-11.2.0/gcc/Common-Type-Attributes.html  
However, if you set `-std=c++17`, you get a different error:  
```  
/opt/compiler-explorer/libs/boost_1_77_0/boost/pfr/detail/core17_generated.hpp:58:57: error: cannot bind packed field 'a' to 'int&'  
   58 |   return ::boost::pfr::detail::make_tuple_of_references(a,b,c);  
      |                                                         ^  
```  
Which reveals a deeper problem, namely that gcc doesn't allow `int& x = a.x;`  
Clang actually allows this (your example compiles with clang in c++17), and even gcc allows `int* x = &a.x;` with a warning, but you shouldn't do that either, as dereferencing an unaligned pointer is undefined behavior. Packed structs should be treated the same as bitfields, you have to access the members through the structure as you can't get pointers/references to the members. (Unless there's yet another gcc extension, that allows you to mark a pointer as unaligned).  
  
By the way, bitfields don't work either.

---

## Comment 2

> Username: technic  
> Created at: 2021-11-09 21:11:56 UTC  
> Url: https://github.com/boostorg/pfr/issues/93#issuecomment-964547641  

It is good that gcc forbids the creating reference to unaligned structure field. Clang does not, and this would likely result with ub on some platforms.  
  
The assertion from the loophole implementation makes perfect sense, and I think we may need a similar assertion for the cpp17 structure binding implementation.  
  
Given that there is no way to obtain a reference to custom aligned field, it's good to have an option access members by value.   
Interestingly, the gcc does not give a warning for the corresponding `structure_to_tuple` method. Logically in this case there is no reference returned outside, but there is some base classes magic in detail/sequence_tuple.hpp which I didn't get. Aren't they accessing by reference in the middle?

---

## Comment 3

> Username: technic  
> Created at: 2021-11-09 21:53:44 UTC  
> Url: https://github.com/boostorg/pfr/issues/93#issuecomment-964580665  

Update:  
  
So the `structure_to_tuple` works because it binds by constant reference (actually it's happening in `tie_as_tuple()`), and in case of gcc it seem to copy the value. See small example here https://godbolt.org/z/1f9nKx4bd

---

## Comment 4

> Username: technic  
> Created at: 2021-11-09 22:00:44 UTC  
> Url: https://github.com/boostorg/pfr/issues/93#issuecomment-964585423  

also see the following clang bug https://bugs.llvm.org/show_bug.cgi?id=28571

---

## Comment 5

> Username: grybouilli  
> Created at: 2025-07-11 07:02:56 UTC  
> Url: https://github.com/boostorg/pfr/issues/93#issuecomment-3060895029  

Found [this trick](https://www.reddit.com/r/cpp/comments/1awgx7z/struct_packing_without_gnupacked_using_boostendian/):  
  
```cpp  
struct packed {  
    uint8_t foo;  
    boost::endian::native_uint32_t bar;  
};  
  
static_assert(sizeof(packed) == 5); // OK  
  
int main()  
{  
    auto s = packed {8, 32};  
    constexpr auto names = boost::pfr::names_as_array<decltype(s)>();  
    boost::pfr::for_each_field(s, [&names](const auto& field, size_t idx) { std::print("{}: {}\n", names[idx], field); });  
}  
```  
  
Complete [demo here](https://godbolt.org/z/dTdTjheze).  
  
I don't know how this would work for serialization tho.
