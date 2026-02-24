# #69 - boost::mpl::integral_c broken in Clang trunk [Closed]

> Username: jyknight  
> Created at: 2022-08-04 22:12:10 UTC  
> Updated at: 2025-02-26 08:23:50 UTC  
> Closed at: 2024-06-18 00:12:58 UTC  
> Url: https://github.com/boostorg/mpl/issues/69  

Clang has recently [addressed](https://reviews.llvm.org/D130058) an [issue](https://github.com/llvm/llvm-project/issues/50055) where it failed to diagnose the UB of casting an out-of-range integer to an enum in a constant expression.  
  
Since undefined behavior is not allowed in a constant expression, Clang now prohibits `enum test { zero }; constexpr int g = static_cast<test>(-1);` with "error: constexpr variable 'g' must be initialized by a constant expression; note: integer value -1 is outside the valid range of values [0, 1] for this enumeration type"  
  
This change breaks boost::mpl::integral_c, since it does exactly that. Demonstrated with example, below ([and on godbolt](https://godbolt.org/z/Gf3fvGvPT))  
```  
#include <boost/mpl/integral_c.hpp>  
enum test { zero, one };  
boost::mpl::integral_c<test, zero> m;  
```  
  
Originally discovered with a program that was using the boost::numeric library, which in turn uses mpl like the above example.  
```  
#include <boost/numeric/conversion/udt_builtin_mixture.hpp>  
boost::numeric::udt_builtin_mixture<int, int> m;  
```

---

## Comment 1

> Username: dabrahams  
> Created at: 2022-08-09 18:23:44 UTC  
> Url: https://github.com/boostorg/mpl/issues/69#issuecomment-1209723082  

The fix looks like [this](https://godbolt.org/z/WY7e6o1WW).

---

## Comment 2

> Username: dabrahams  
> Created at: 2022-08-10 02:19:23 UTC  
> Url: https://github.com/boostorg/mpl/issues/69#issuecomment-1210073544  

Actually I'm back to believing there's no true fix if next and prior have to be of type `integral_c<T, ...>`.

---

## Comment 3

> Username: jyknight  
> Created at: 2022-08-16 17:59:15 UTC  
> Url: https://github.com/boostorg/mpl/issues/69#issuecomment-1216969789  

Perhaps the correct fix is to somehow cause the next/prior members to be omitted when they're impossible to represent, just like they're omitted for bool? I expect that's possible somehow via some template specialization magic.  
  
  
Note that Clang has been changed since a week ago to no longer have this be an unconditional error. Now it's a controllable warning `-Wenum-constexpr-conversion`, which by default is enabled as an error. However, it's suppressed in system headers, and can be downgraded/disabled in user code as well.  So, to see the error in the godbolt example you'll now need to add `-Wsystem-headers` to the compiler options. (e.g. https://godbolt.org/z/v4PPjW1n1)  
  
Since most code using boost will be using it as a system header, they'll also not see the error, and anyone else may downgrade the error to a warning or disable it entirely if desired. However, it may become an unconditional error again in some future release of Clang, so it'd still be good to address it.

---

## Comment 4

> Username: dabrahams  
> Created at: 2022-08-16 19:48:23 UTC  
> Updated at: 2022-08-16 19:48:46 UTC  
> Url: https://github.com/boostorg/mpl/issues/69#issuecomment-1217090701  

> Perhaps the correct fix is to somehow cause the next/prior members to be omitted when they're impossible to represent, just like they're omitted for bool? I expect that's possible somehow via some template specialization magic.  
  
Well, that's what I investigated, and my (ill-informed) conclusion is that it's not possible.  The problem is that the the only expressions that try to create the representation (`static_cast<test>(-1)`) are not constant expressions, and they're well-typed.  I hope somebody proves me wrong, but I'm certainly out of ideas.

---

## Comment 5

> Username: shafik  
> Created at: 2022-08-23 17:43:07 UTC  
> Url: https://github.com/boostorg/mpl/issues/69#issuecomment-1224442828  

If the intent is that the enum should be able to handle values outside the enumerator values then perhaps making it have a fixed underlying type better represents the intent?

---

## Comment 6

> Username: dabrahams  
> Created at: 2022-08-23 18:09:44 UTC  
> Url: https://github.com/boostorg/mpl/issues/69#issuecomment-1224511124  

I don't think that's the intent, but it is a decent workaround for the problem.  I don't believe the intent is actually realizable, but like I said, I no longer really know C++, so I might be wrong.

---

## Comment 7

> Username: ecatmur  
> Created at: 2022-11-16 01:36:43 UTC  
> Url: https://github.com/boostorg/mpl/issues/69#issuecomment-1316152037  

the `static const` trick works: https://godbolt.org/z/KrhdzqYbx  
```c++  
enum test { zero, one };  
template<class E, E a>  
E const k = static_cast<E>(a - 1);  
template<class T, T> struct S {};  
template<class> int f();  
int g() { return f<S<test, k<test, zero>>>(); }  
```  
this means that going all the way back to the EDG implementation/workaround actually "fixes" it: https://godbolt.org/z/8bTerz3WM  
```c++  
#define __EDG_VERSION__ 242  
#include <boost/mpl/integral_c.hpp>  
enum test { zero, one };  
boost::mpl::integral_c<test, zero> m;  
```

---

## Comment 8

> Username: smeenai  
> Created at: 2023-10-16 19:59:58 UTC  
> Url: https://github.com/boostorg/mpl/issues/69#issuecomment-1765181571  

Was there any progress here? Clang landed https://github.com/llvm/llvm-project/pull/67528 which makes this an error by default even in system headers. We can turn off the warning for now, but that option will be removed in a future Clang release, so this will need to be addressed before then.

---

## Comment 9

> Username: carlosgalvezp  
> Created at: 2023-11-24 14:44:41 UTC  
> Url: https://github.com/boostorg/mpl/issues/69#issuecomment-1825771503  

Hi! Any progress? What is the ETA for fixing? Next Clang release in about 6 months will turn the warning into a non-suppressable hard error.

---

## Comment 10

> Username: kencu  
> Created at: 2023-12-20 19:04:11 UTC  
> Url: https://github.com/boostorg/mpl/issues/69#issuecomment-1864992847  

we are running into this issue now on MacPorts. current boost builds are failing with the now-default compiler, clang-16.  
  
various patches are being suggested, versions of the ones listed in this PR, but if at all possible, we'd like to go with the one that will be finally integrated into boost.

---

## Comment 11

> Username: EduPonz  
> Created at: 2024-02-17 08:33:23 UTC  
> Url: https://github.com/boostorg/mpl/issues/69#issuecomment-1949900133  

We have hit the same issue (see [here](https://github.com/eProsima/Fast-DDS/actions/runs/7934205654/job/21664653765?pr=4100#step:10:1987). It seems that macports/macports-ports#21839 does not fix it for us.

---

## Comment 12

> Username: rpopescu  
> Created at: 2024-06-17 17:39:02 UTC  
> Url: https://github.com/boostorg/mpl/issues/69#issuecomment-2173972117  

Does anyone know what the latest status is, for example, which is the earliest version of boost where this is fixed?  
  
tagging @pdimov with thanks!

---

## Comment 13

> Username: pdimov  
> Created at: 2024-06-18 00:13:38 UTC  
> Url: https://github.com/boostorg/mpl/issues/69#issuecomment-2174666767  

It will be fixed in the next release, 1.86.

---

## Comment 14

> Username: rpopescu  
> Created at: 2024-06-21 16:28:40 UTC  
> Url: https://github.com/boostorg/mpl/issues/69#issuecomment-2183066965  

Thank you!

---

## Comment 15

> Username: Artalus  
> Created at: 2024-08-21 08:04:25 UTC  
> Url: https://github.com/boostorg/mpl/issues/69#issuecomment-2301411819  

We are currently unable to update our Boost to 1.86 - but can patch and rebuild the version we currently use, and would like to backport the needed changes locally, to build our 1.75 with newer Clang. I checked the history of this issue, and it seems that the combination of  
  
- this change in `numeric`: https://github.com/boostorg/numeric_conversion/commit/50a1eae942effb0a9b90724323ef8f2a67e7984a  
  
- and this change in MPL: https://github.com/boostorg/mpl/pull/77  
  
does fix the build error for us. Since we are using Conan to provide third-party libraries, I also thought it might be a good idea to share such patch with a bigger audience.  
@pdimov apologies, but could you maybe confirm if [this patch](https://github.com/conan-io/conan-center-index/pull/24967/files#diff-400b59cc4a38af4bdd393f5fdb3e6086e809abb9508b3bb017e6dd854a2d4676) is indeed sufficient for a fix, and does not miss anything not mentioned in this thread?

---

## Comment 16

> Username: pdimov  
> Created at: 2024-08-21 08:16:28 UTC  
> Url: https://github.com/boostorg/mpl/issues/69#issuecomment-2301439492  

I think that these two patches are sufficient, yes.

---

## Comment 17

> Username: piersh-aetheros  
> Created at: 2025-01-25 21:53:56 UTC  
> Url: https://github.com/boostorg/mpl/issues/69#issuecomment-2614111353  

can this change be backported? many distros aren't shipping recent versions of boost yet. for example, RedHat 9 has Clang 18 and boost 1.75.0.

---

## Comment 18

> Username: Egorundel  
> Created at: 2025-02-26 08:23:48 UTC  
> Url: https://github.com/boostorg/mpl/issues/69#issuecomment-2684257955  

Hello, guys!  
  
Please, help me.   
  
I don't understand what the problem is and how to fix it. Attached screenshots of errors.  
  
![Image](https://github.com/user-attachments/assets/19a3e901-967a-494d-9996-3b49530327d8)  
![Image](https://github.com/user-attachments/assets/28be4882-9124-4d54-8c21-e8e15f5e0595)  
![Image](https://github.com/user-attachments/assets/8aefd46c-8cb3-43c7-b7ee-4bd2a5044b61)  
  
I work in Qt Creator. It seems like I’m working with the GCC compiler, but for some reason I’m getting errors due to clang...  
  
![Image](https://github.com/user-attachments/assets/974a734d-0b0b-41cb-847b-2abc79ae43db)  
  
Here is my version of Boost in the screenshot below (1.74.0):  
  
![Image](https://github.com/user-attachments/assets/ebc6d50c-eae6-4219-936e-7cdd4b1028af)  
  
Ubuntu version: 22.04
