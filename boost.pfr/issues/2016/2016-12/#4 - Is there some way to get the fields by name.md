# #4 - Is there some way to get the fields by name? [Closed]

> Username: qicosmos  
> Created at: 2016-12-08 23:37:03 UTC  
> Updated at: 2020-11-10 20:09:57 UTC  
> Closed at: 2018-10-09 18:58:56 UTC  
> Url: https://github.com/boostorg/pfr/issues/4  

magic_get is really magic, however magic_get can only get fileds by index, sometimes i need get fields by name, is there some way to get the fields by name?

---

## Comment 1

> Username: apolukhin  
> Created at: 2017-01-18 19:26:43 UTC  
> Url: https://github.com/boostorg/pfr/issues/4#issuecomment-273574844  

Unfortunately, currently there's no known way to do that. I'd appreciate any suggestions.

---

## Comment 2

> Username: AshMcConnell  
> Created at: 2018-02-22 19:39:32 UTC  
> Url: https://github.com/boostorg/pfr/issues/4#issuecomment-367796213  

A slightly different, but related, question, is it possible to get the name of the fields as you are iterating (I'm thinking for a JSON parser)?

---

## Comment 3

> Username: willwray  
> Created at: 2018-03-02 03:13:43 UTC  
> Url: https://github.com/boostorg/pfr/issues/4#issuecomment-369807334  

Not without the magic in the reflection proposals (you can play with reflection now on experimental compiler branches though it is unlikely to land in the standard any time soon).  
  
Of course, if you are willing to (1) 'register' the members of the struct, and (2) accept some non-standard C++, then it is possible to get the names. A nice way to do this, without macros, is by specialising a type (or variable) template with a list of pointers to the struct members as template arguments:  
  
```  
// Primary template, a variable template to 'register' member pointers  
template <class S> constexpr Members<> members{};  
  
// Structure to reflect on  
struct S { int a; char b[4]; };  
  
// Specialise 'member' variable template to register S and its members:  
template<> constexpr auto members<S> = Members<&S::a,&S::b>{};  
```  
(C++11 support calls for class specialization - more verbose but more flexible than C++14 variable templates.)  
  
With this extra declaration, when given an object of type S, a getter can test for the existence of the `members<S>` specialization and then use it for tuple-like access. As an extra bonus, this access is constexpr so you can constexpr all the things in your JSON parser (for compile-time usage a 'compile time string' is needed - `std::array<char>` will do).  
  
Note that magic_get can be used to 'type check' the completeness and consistency of the `Members<&S::name...>` type. Irritatingly, though, it is not possible to check that the correct corresponding struct is being registered in the `members<S>` variable (`members<Foo>` is equally valid) - an extra line is needed for this extra check.   
  
To extract the names from the member-pointer template arguments you have to stray from portable standard C++; Mr Polukhin is a past master of such trickery.  
  
So, not totally magic but 'registering' has real practical benefits.

---

## Comment 4

> Username: apolukhin  
> Created at: 2018-10-09 18:58:56 UTC  
> Url: https://github.com/boostorg/pfr/issues/4#issuecomment-428310424  

I'm closing this issue because I still fail to find a way to get field name without manual registration. The whole idea of the library is to avoid any registration, so I'd rather not open that can.  
  
Feel free to reopen it if some new trick gets discovered.

---

## Comment 5

> Username: pavelkryukov  
> Created at: 2020-11-10 12:56:33 UTC  
> Updated at: 2020-11-10 17:51:27 UTC  
> Url: https://github.com/boostorg/pfr/issues/4#issuecomment-724684612  

> Unfortunately, currently there's no known way to do that. I'd appreciate any suggestions.  
  
Hi! Just for fun, I did an [interchangeable AoS/SoA container](https://github.com/pavelkryukov/AoAoAoTT). I solved a problem of getting a member by pointer-to-member with code listed below. Unfortunately, it is UB, but may provide some thoughts:  
  
`get_container_impl` returns a SoA sub-container, but it may use `boost::prf::get<I-1>` as well  
  
https://github.com/pavelkryukov/AoAoAoTT/blob/ac69898db44806a25ecda88cbee045495a639df8/aoaoaott.hpp#L300-L331

---

## Comment 6

> Username: pavelkryukov  
> Created at: 2020-11-10 20:09:56 UTC  
> Url: https://github.com/boostorg/pfr/issues/4#issuecomment-724938764  

Oops, it seems I've tried to fix a different problem here, which is trivially solved by `val.field` syntax.  
  
Do I understand correctly that the idea is to have something like `boost::prf::get(value, "field")`?  
Probably, it may be registered automatically with something similar to MagicEnum.
