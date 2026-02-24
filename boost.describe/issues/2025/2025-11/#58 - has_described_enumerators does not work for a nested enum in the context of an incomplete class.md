# #58 - has_described_enumerators does not work for a nested enum in the context of an incomplete class [Closed]

> Username: Julien-Blanc-tgcm  
> Created at: 2025-11-07 09:56:04 UTC  
> Updated at: 2025-11-21 18:28:32 UTC  
> Closed at: 2025-11-21 18:27:46 UTC  
> Url: https://github.com/boostorg/describe/issues/58  

`has_described_enumerators` cannot be used on a nested enum if the class where it is defined is incomplete.  
  
This is a minimal example reproducing the issue:  
  
```cpp  
#include <boost/describe.hpp>  
  
struct Foo  
{  
    enum Bar { b1, b2 };  
    BOOST_DESCRIBE_NESTED_ENUM(Bar, b1, b2);  
    static_assert(boost::describe::has_describe_enumerators<Foo::Bar>::value);  
};  
```  
leads to   
```  
error: static assertion failed due to requirement 'boost::describe::has_describe_enumerators<B::Enum, void>::value'  
    7 |         static_assert(boost::describe::has_describe_enumerators<Enum>::value);  
      |                       ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
```  
  
The following is ok  
```cpp  
#include <boost/describe.hpp>  
  
struct Foo  
{  
    enum Bar { b1, b2 };  
    BOOST_DESCRIBE_NESTED_ENUM(Bar, b1, b2);  
};  
static_assert(boost::describe::has_describe_enumerators<Foo::Bar>::value);  
```

---

## Comment 1

> Username: Julien-Blanc-tgcm  
> Created at: 2025-11-07 14:05:55 UTC  
> Url: https://github.com/boostorg/describe/issues/58#issuecomment-3502758024  

I've made a quick patch for that, but it requires C++20. I'm not sure it's doable in previous C++ versions.  
  
Since this issue is mostly annoying when using concepts, would that be ok for you ? (pre C++20 code would rely on existing implementation and still have this issue, C++20 and later would be fixed).

---

## Comment 2

> Username: pdimov  
> Created at: 2025-11-07 15:51:08 UTC  
> Url: https://github.com/boostorg/describe/issues/58#issuecomment-3503319262  

May I see the fix?

---

## Comment 3

> Username: Julien-Blanc-tgcm  
> Created at: 2025-11-08 11:11:49 UTC  
> Url: https://github.com/boostorg/describe/issues/58#issuecomment-3506452121  

See https://github.com/boostorg/describe/compare/develop...Julien-Blanc-tgcm:describe:Fix_Describe_Enumerators_CPP20 for a POC (not a production ready fix).  
  
My understanding is that the compiler complains about boost_enum_descriptor_fn being not fully defined if used inside the class that defines it. But the only thing needed for `has_describe_enumerators` is its return type, which can be given. The whole purpose of the patch is to make that information available. But it relies on C++20 (template on a value of a structural type).

---

## Comment 4

> Username: pdimov  
> Created at: 2025-11-08 12:20:35 UTC  
> Url: https://github.com/boostorg/describe/issues/58#issuecomment-3506497219  

That's a bit too much. Maybe just define another friend function whose only purpose is to signal "this is described"?

---

## Comment 5

> Username: Julien-Blanc-tgcm  
> Created at: 2025-11-09 07:22:35 UTC  
> Url: https://github.com/boostorg/describe/issues/58#issuecomment-3507600489  

You're right, it's a lot simpler that way and also works with C++ < 20. I've made a PR.

---

## Comment 6

> Username: Julien-Blanc-tgcm  
> Created at: 2025-11-14 09:10:57 UTC  
> Url: https://github.com/boostorg/describe/issues/58#issuecomment-3531715959  

After more testing, the two fixes are really not equivalent.  
  
The friend function works for simple assertion on `has_describe_enumerators`, but any attempt to do something with the nested enum would still fail.  
  
OTOH, the initial POC i proposed allows full usage of the reflection capabilities inside the class that defines the nested enum. A typical case is when instantiating a template class that relies on the enum being describe-d.

---

## Comment 7

> Username: pdimov  
> Created at: 2025-11-14 11:48:49 UTC  
> Url: https://github.com/boostorg/describe/issues/58#issuecomment-3532378886  

Yes, you are right. But an easier fix seems to be possible if we have C++20, because it allows lambdas in unevaluated contexts. I'm on it.

---

## Comment 8

> Username: pdimov  
> Created at: 2025-11-14 12:02:48 UTC  
> Url: https://github.com/boostorg/describe/issues/58#issuecomment-3532421781  

Unfortunately, even though it works on MSVC and Clang, GCC hates it.  
```  
./boost/describe/enum.hpp:51:66: error: types may not be defined in 'decltype' expressions  
   51 | #define BOOST_DESCRIBE_ENUM_ENTRY(E, e) , []{ struct _boost_desc { \  
      |                                                                  ^  
```

---

## Comment 9

> Username: pdimov  
> Created at: 2025-11-14 12:13:40 UTC  
> Url: https://github.com/boostorg/describe/issues/58#issuecomment-3532458218  

Let's see what they say: https://gcc.gnu.org/bugzilla/show_bug.cgi?id=122688

---

## Comment 10

> Username: Julien-Blanc-tgcm  
> Created at: 2025-11-14 12:29:54 UTC  
> Url: https://github.com/boostorg/describe/issues/58#issuecomment-3532517325  

Apparently it's an old and already reported gcc bug : https://gcc.gnu.org/bugzilla/show_bug.cgi?id=101911

---

## Comment 11

> Username: pdimov  
> Created at: 2025-11-14 12:38:02 UTC  
> Url: https://github.com/boostorg/describe/issues/58#issuecomment-3532547842  

Three times, even. Well, it doesn't hurt to report it for the fourth time. :-)

---

## Comment 12

> Username: Julien-Blanc-tgcm  
> Created at: 2025-11-14 13:39:41 UTC  
> Url: https://github.com/boostorg/describe/issues/58#issuecomment-3532862241  

What do you think of https://github.com/Julien-Blanc-tgcm/describe/commit/20ad6756d532733155c7a461cb16ea7405350f0a  
  
Seems ok with gcc and clang (don't have msvc to check). Requires c++20, but that's a lot less changes that my initial POC. Need to #ifdef CPP20 it correctly if you're ok with the approach.

---

## Comment 13

> Username: pdimov  
> Created at: 2025-11-14 14:02:44 UTC  
> Url: https://github.com/boostorg/describe/issues/58#issuecomment-3532952282  

This looks promising. Applied here https://github.com/boostorg/describe/commit/b1b31d4afe05563d435c6fcbd608987a46b34679, let's see what CI says.

---

## Comment 14

> Username: Julien-Blanc-tgcm  
> Created at: 2025-11-14 14:36:08 UTC  
> Url: https://github.com/boostorg/describe/issues/58#issuecomment-3533083591  

Looks like more fine-grained checking must be done. I think the proper test should be on `__cpp_nontype_template_args`, but clang keeps reporting it at 201411L even on modern versions.  
  
The best way is probably to disable that feature for clang-10-12 as well.

---

## Comment 15

> Username: pdimov  
> Created at: 2025-11-21 18:28:31 UTC  
> Url: https://github.com/boostorg/describe/issues/58#issuecomment-3564173589  

The fix is on develop now.
