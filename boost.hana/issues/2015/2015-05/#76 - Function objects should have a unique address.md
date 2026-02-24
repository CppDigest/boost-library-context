# #76 - Function objects should have a unique address [Closed]

> Username: ldionne  
> Created at: 2015-05-20 19:45:09 UTC  
> Updated at: 2015-09-27 17:10:50 UTC  
> Closed at: 2015-09-27 17:10:50 UTC  
> Url: https://github.com/boostorg/hana/issues/76  

When used from different translation units, function objects should resolve to the same address. In other words, we want `boost::hana::drop` to have the same address in all translation units. This is related to #75.

---

## Comment 1

> Username: vtnerd  
> Created at: 2015-05-20 20:00:04 UTC  
> Url: https://github.com/boostorg/hana/issues/76#issuecomment-104017537  

Eric Niebler discussed the same thing in [this paper](http://ericniebler.github.io/std/wg21/D4381.html). I think the technique should work for Hana too.

---

## Comment 2

> Username: ldionne  
> Created at: 2015-05-20 20:06:34 UTC  
> Url: https://github.com/boostorg/hana/issues/76#issuecomment-104020032  

Thanks _a lot_ for the pointer. I was aware of his blog post on the matter, but not this extended explanation.

---

## Comment 3

> Username: ldionne  
> Created at: 2015-05-21 16:35:26 UTC  
> Url: https://github.com/boostorg/hana/issues/76#issuecomment-104346652  

I am in the process of fixing this bug. Here's my current solution, which does not quite work:  
  
Normally, I would have  
  
``` c++  
struct _drop { };  
constexpr _drop drop{};  
```  
  
Following @ericniebler's article, my fix goes like:  
  
``` c++  
template <typename T>  
constexpr T static_constexpr{};  
  
struct _drop { };  
namespace { constexpr auto const& drop = static_constexpr<_drop>; }  
```  
  
However, the addresses of `drop` still appear to be different when fetched from different translation units, which is incorrect. Any clue what is going on? More precisely, here's a minimal working example showing my issue:  
  
### In `a.cpp`  
  
``` c++  
#include <iostream>  
  
template <typename T>  
constexpr T static_constexpr{};  
  
struct _drop { };  
namespace { constexpr auto const& drop = static_constexpr<_drop>; }  
  
void show_pointers(void const* p) {  
    std::cout << p << std::endl;  
    std::cout << &drop << std::endl;  
}  
```  
  
### In `b.cpp`  
  
``` c++  
template <typename T>  
constexpr T static_constexpr{};  
  
struct _drop { };  
namespace { constexpr auto const& drop = static_constexpr<_drop>; }  
  
void show_pointers(void const* p);  
  
int main() {  
    show_pointers(&drop);  
}  
```  
  
And the output is   
  
``` sh  
0x107344f22  
0x107344f23  
```

---

## Comment 4

> Username: pfultz2  
> Created at: 2015-05-21 16:50:38 UTC  
> Updated at: 2015-05-21 16:54:10 UTC  
> Url: https://github.com/boostorg/hana/issues/76#issuecomment-104352908  

I am implementing this currently in my Fit library(and it works), but I believe you need to declare a static member. For function objects, you should be able to do this:  
  
``` cpp  
template<class T>  
struct static_const  
{  
    static constexpr T value {};  
};  
  
template<class T>  
constexpr T static_const<T>::value;  
  
struct _drop { };  
static constexpr const auto& drop = static_const<_drop>::value;  
```  
  
You can see the source [here](https://github.com/pfultz2/Fit/blob/static_const/fit/function.h) for `FIT_STATIC_FUNCTION`(I don't know how easy it is to follow, since I do extra things to ensure that lambdas have unique addresses across TUs as well).   
  
You could also look at how Eric Niebler's range library implements this(he has a [`static_const`](https://github.com/ericniebler/range-v3/blob/master/include/range/v3/utility/static_const.hpp) class for declaring function objects).

---

## Comment 5

> Username: ldionne  
> Created at: 2015-05-21 17:47:59 UTC  
> Url: https://github.com/boostorg/hana/issues/76#issuecomment-104369660  

I can confirm that your suggestion works, but I find this curious. This means that variable templates resolve to different addresses, yet nested static members in a template do resolve to the same address.

---

## Comment 6

> Username: ldionne  
> Created at: 2015-05-21 18:07:23 UTC  
> Url: https://github.com/boostorg/hana/issues/76#issuecomment-104374186  

@pfultz2  Do you _actually_ think it is worth going through these hoops to ensure the uniqueness of address for function objects? I'm thinking more and more that this is superfluous and makes the code more complicated for 0 gain.

---

## Comment 7

> Username: vtnerd  
> Created at: 2015-05-21 18:08:49 UTC  
> Updated at: 2015-05-21 18:09:47 UTC  
> Url: https://github.com/boostorg/hana/issues/76#issuecomment-104374596  

From the N3797 draft:  
  
> **3.2.4**  
> Every program shall contain exactly one definition of every non-inline function or variable that is odr-used in that program; no diagnostic required. The definition can appear explicitly in the program, it can be found in the standard or a user-defined library, or (when appropriate) it is implicitly defined (see 12.1, 12.4 and 12.8). An inline function shall be defined in every translation unit in which it is odr-used.   
> ...  
> **3.2.6**  
> There can be more than one definition of a class type (Clause 9), enumeration type (7.2), inline function with external linkage (7.1.2), class template (Clause 14), non-static function template (14.5.6), static data member of a class template (14.5.1.3), member function of a class template (14.5.1.1), or template specialization for which some template parameters are not specified (14.7, 14.5.5) in a program provided that each definition appears in a different translation unit, and provided the definitions satisfy the following requirements.  
  
N4296 has the same language. So a ODR-used variable-template should be a violation of the ODR if it appears in multiple TU. This [EDIT: referring to Paul Fultz suggestion] is _not_ a violation of the ODR, because **3.2.6** provides an exception for a static data member of a class template.  
  
 Or at least thats my interpretation of this fun corner case of the language. Maybe Eric has more information since he wrote a lot more on the subject.

---

## Comment 8

> Username: ldionne  
> Created at: 2015-05-21 18:11:07 UTC  
> Url: https://github.com/boostorg/hana/issues/76#issuecomment-104375305  

Right, so no ODR violation for variable templates, but they still get different addresses. Is this as bad as I make it out to be?

---

## Comment 9

> Username: ericniebler  
> Created at: 2015-05-21 18:38:17 UTC  
> Url: https://github.com/boostorg/hana/issues/76#issuecomment-104382400  

That's pretty bad IMO. Probably worth following up with the committee, or  
at least shooting James Widman an email.  
  
\e  
  
On Thu, May 21, 2015, 11:11 AM Louis Dionne notifications@github.com  
wrote:  
  
> Right, so no ODR violation for variable templates, but they still get  
> different addresses. Is this as bad as I make it out to be?  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/ldionne/hana/issues/76#issuecomment-104375305.

---

## Comment 10

> Username: ericniebler  
> Created at: 2015-05-21 18:43:44 UTC  
> Updated at: 2015-05-21 18:44:57 UTC  
> Url: https://github.com/boostorg/hana/issues/76#issuecomment-104384203  

BTW, the analysis of the ODR in the draft of my paper is wrong. Please see the official version of N4381 [here](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2015/n4381.html). (Never take anything you read in a DXXXX paper as gospel truth. Those are drafts. Always look for the NXXXX paper.)

---

## Comment 11

> Username: ldionne  
> Created at: 2015-05-21 18:48:11 UTC  
> Url: https://github.com/boostorg/hana/issues/76#issuecomment-104385253  

Alright, thanks!

---

## Comment 12

> Username: vtnerd  
> Created at: 2015-05-21 18:48:20 UTC  
> Url: https://github.com/boostorg/hana/issues/76#issuecomment-104385286  

My understanding is that is an ODR violation (thus the multiple addresses for the same object), and `constexpr` implies internal linkage. The compiler/linker provides no error, as per **3.2.4**, since the internal linkage indicates that the objects are different. Joel de Guzman came to the [same conclusion](http://boost.2283326.n4.nabble.com/Global-objects-in-x3-td4675784.html) on a similar issue in X3. But as Eric _just_ said, maybe someone with better knowledge on the subject should be asked. FWIW, using the address of a hana function does seem like a rare case.

---

## Comment 13

> Username: ldionne  
> Created at: 2015-05-21 18:49:27 UTC  
> Url: https://github.com/boostorg/hana/issues/76#issuecomment-104385500  

Actually, it seems like it should never happen. Hence, I am left wondering whether it is at all useful to try and provide unique addresses. As you can see in b5860b7, it bloats the code quite a bit.

---

## Comment 14

> Username: ldionne  
> Created at: 2015-05-21 18:56:25 UTC  
> Url: https://github.com/boostorg/hana/issues/76#issuecomment-104388421  

Crap: a variable is ODR-used when it is passed as a reference to a function. Hence, if you take Hana function object by reference in two different translation units and then link them together, this is an ODR violation IIUC.

---

## Comment 15

> Username: pfultz2  
> Created at: 2015-05-21 19:09:23 UTC  
> Url: https://github.com/boostorg/hana/issues/76#issuecomment-104391522  

> @pfultz2 Do you actually think it is worth going through these hoops to ensure the uniqueness of address for function objects?  
  
For a general-purpose library, yes. If the compiler doesn't inline the function object, it can bloat the final executable with multiple copies of the same function object(because there is multiple addresses).  
  
> Crap: a variable is ODR-used when it is passed as a reference to a function. Hence, if you take Hana function object by reference in two different translation units and then link them together, this is an ODR violation IIUC.  
  
Oh, that is probably another reason.

---

## Comment 16

> Username: pfultz2  
> Created at: 2015-05-21 19:21:30 UTC  
> Url: https://github.com/boostorg/hana/issues/76#issuecomment-104393798  

@ericniebler In the paper it says:  
  
> The anonymous namespace is needed to keep the std::begin reference itself from being multiply defined.  
  
Wouldn't `static` work as well? It seems to work on gcc and clang using `static`, ie:  
  
``` cpp  
static constexpr auto const& begin = __static_const<__detail::__begin_fn>;  
```

---

## Comment 17

> Username: ldionne  
> Created at: 2015-05-21 20:17:10 UTC  
> Url: https://github.com/boostorg/hana/issues/76#issuecomment-104408219  

> For a general-purpose library, yes. If the compiler doesn't inline the function object, it can bloat the final  executable with multiple copies of the same function object(because there is multiple addresses).  
  
I don't expect anybody to actually store the addresses of those function objects. Furthermore, since those function objects are empty, I would expect the compiler to optimize away argument passing via reference (which would normally require the address of the function object). Hence, I don't think code bloat is an issue.

---

## Comment 18

> Username: pfultz2  
> Created at: 2015-05-21 20:41:02 UTC  
> Url: https://github.com/boostorg/hana/issues/76#issuecomment-104414574  

> I don't expect anybody to actually store the addresses of those function objects.  
  
It doesn't matter. The compiler could still choose not to inline the function even if you don't take the address(for example if you use `-fno-inline` or the user reached some internal limit with inlining).  
  
> Furthermore, since those function objects are empty  
  
Empty != 0. Each object will take at least one byte or more in the final executable depending on binary formats.  
  
Eric's solution is pretty simple and easy to apply everywhere(at least for function objects) to avoid these issues.

---

## Comment 19

> Username: ldionne  
> Created at: 2015-05-21 20:46:24 UTC  
> Url: https://github.com/boostorg/hana/issues/76#issuecomment-104416000  

Ok, I agree this needs to be fixed, but Eric's fix is nowhere near "easy to apply everywhere" for Hana, which uses variable templates and `decltype` in conjunction (I don't want `decltype(type<T>)` to be a reference, for example): https://travis-ci.org/ldionne/hana/jobs/63536584#L1484  
  
Also, Clang 3.5 seems to have issues: https://travis-ci.org/ldionne/hana/jobs/63536579#L704

---

## Comment 20

> Username: pfultz2  
> Created at: 2015-05-21 21:05:33 UTC  
> Url: https://github.com/boostorg/hana/issues/76#issuecomment-104420032  

> I don't want decltype(type<T>) to be a reference  
  
That is a good point. It seems like it would be easier to make `type<T>` a class instead of a variable template. Then again, if concepts lite goes the route of using variable templates, they may tweak the language to make this problem go away, anyway.

---

## Comment 21

> Username: ldionne  
> Created at: 2015-05-21 21:12:45 UTC  
> Url: https://github.com/boostorg/hana/issues/76#issuecomment-104421933  

> That is a good point. It seems like it would be easier to make `type<T>` a class instead of a variable template.  
  
Yes, it would solve a couple of problems, but we would have to write `type<T>{}` all around the place instead of `type<T>`, which is not only uglier but also defeats the "philosophy" of Hana which is "types as values". We would then have the same problem for `integral_constant`, `metafunction` and all the other related constructs. I guess this idea is worth considering, but I'm a bit reluctant to bastardize Hana's interface for a corner case issue like this.  
  
By the way, my (naive) point of view on the whole issue is that global objects defined in header files should simply be collapsed into a single object by the linker, without creating a ODR violation. I know the language won't change since it might break existing code, but to me it seems like a flaw in the language rather than in the design of the library.

---

## Comment 22

> Username: viboes  
> Created at: 2015-05-21 21:26:33 UTC  
> Url: https://github.com/boostorg/hana/issues/76#issuecomment-104425368  

You must design a library following the rules of the languages, otherwise the library is bad designed.  
If the language is bad designed, it should be changed.

---

## Comment 23

> Username: pfultz2  
> Created at: 2015-05-21 21:31:41 UTC  
> Updated at: 2015-05-22 13:59:10 UTC  
> Url: https://github.com/boostorg/hana/issues/76#issuecomment-104426205  

> By the way, my (naive) point of view on the whole issue is that global objects defined in header files should simply be collapsed into a single object by the linker  
  
The linker will do this for template functions and classes. It doesn't do it for global variables, though.   
  
> I know the language won't change since it might break existing code  
  
Well, I don't know if that is true. It would be nice if the language at least made global `constexpr` objects unique and ODR-friendly, but then again there is [this](http://b.atch.se/posts/non-constant-constant-expressions/).

---

## Comment 24

> Username: JamesWidman  
> Created at: 2015-05-22 07:28:47 UTC  
> Url: https://github.com/boostorg/hana/issues/76#issuecomment-104546320  

There is an open issue related to this for the core language; for future reference it is:   
  
"2104. Internal-linkage constexpr references and ODR requirements"  
  
... which, in the next week-ish, should appear in the new version of the issues list here:  
  
http://www.open-std.org/JTC1/SC22/WG21/docs/cwg_active.html  
  
I think there is consensus (in the core working group) that an implementation should be able to look through the reference to the object in a case like this; a note about that should appear when the issues list is updated.  
  
I'm less certain about the expected object identity for variable template instantiation in different translation units; I'll get back to you on that.

---

## Comment 25

> Username: ldionne  
> Created at: 2015-05-22 23:06:38 UTC  
> Url: https://github.com/boostorg/hana/issues/76#issuecomment-104795828  

@JamesWidman Thanks for the additional info.

---

## Comment 26

> Username: MarisaKirisame  
> Created at: 2015-09-05 02:28:47 UTC  
> Url: https://github.com/boostorg/hana/issues/76#issuecomment-137895181  

I have this problem too. Are there any workaround?

---

## Comment 27

> Username: ldionne  
> Created at: 2015-09-05 14:21:09 UTC  
> Url: https://github.com/boostorg/hana/issues/76#issuecomment-137959954  

You can use the trick explained in @pfultz2 's [comment above](https://github.com/ldionne/hana/issues/76#issuecomment-104352908).

---

## Comment 28

> Username: MarisaKirisame  
> Created at: 2015-09-05 15:54:41 UTC  
> Url: https://github.com/boostorg/hana/issues/76#issuecomment-137970509  

@ldionne Thx. I just merge all of my cpp file... Does not bring any issue except compile time.
