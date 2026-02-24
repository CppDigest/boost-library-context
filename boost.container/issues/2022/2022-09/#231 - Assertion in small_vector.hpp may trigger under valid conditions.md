# #231 - Assertion in small_vector.hpp may trigger under valid conditions [Closed]

> Username: whitec20  
> Created at: 2022-09-14 17:28:32 UTC  
> Updated at: 2024-07-07 18:03:11 UTC  
> Closed at: 2024-07-07 18:03:11 UTC  
> Url: https://github.com/boostorg/container/issues/231  

The following assertion inside the two internal_storage() member functions of small_vector_allocator<> will errantly trigger under certain conditions on boost version 1.80.0.  
  
`  
BOOST_ASSERT((std::size_t(this) % dtl::alignment_of<strawman_t>::value) == 0);  
`  
  
It appears offending assertion was introduced by https://github.com/boostorg/container/issues/218  
  
Executing the following within the debugger in Visual Studio 2019 as a Debug x86 build variant will trigger the assertion:  
  
`small_vector<int64_t, 1> v;  
v.push_back(1);`  
  
I've gathered that this explanation on the MSDN page for [Conflicts with the x86 compiler](https://docs.microsoft.com/en-us/cpp/build/x64-software-conventions?view=msvc-160#conflicts-with-the-x86-compiler) is responsible. This stack variable will end up with a pointer that's 4-byte aligned on the stack, even though alignment_of<> and similar functions will return that the natural alignment of the type is 8-bytes.  
  
Separately, it might be a good idea to add a static assertion to these two methods. They cast a small_vector_allocator<> pointer to a vector_type pointer (they seem to be assuming that the allocator will always be the 1st data member of vector_type). In order for that to be safe, vector_type has to satisfy the std::is_standard_layout<> trait.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2022-09-22 07:03:08 UTC  
> Url: https://github.com/boostorg/container/issues/231#issuecomment-1254613045  

Thanks for the report. I still missing some details, though. This means that the compiler does not respect the alignment of a type when it's placed in the stack? And this happens only in x86, but only with msvc, or does this affect gcc and others? Removing the assertion is fine and the unaligned access is harmless?

---

## Comment 2

> Username: thebrandre  
> Created at: 2022-10-18 06:48:23 UTC  
> Url: https://github.com/boostorg/container/issues/231#issuecomment-1281891553  

You can easily reproduce it by compiling this minimalistic example with VS2017's current compiler:  
  
```cpp  
#include <boost/container/small_vector.hpp>  
  
int main() {  
    boost::container::small_vector<double, 8> SmallVector;  
}  
```  
  
Microsoft (R) C/C++ Optimizing Compiler Version 19.16.27048 for x86 aborts the program at runtime with a chance of 50% like this:  
> Assertion failed: (std::size_t(this) % dtl::alignment_of<strawman_t>::value) == 0, file D:\lib\boost_1_80_0\boost/container/small_vector.hpp, line 482  
  
It works with the x64 build. It also works with boost 1.79 and appeared first in 1.80.  
(I used the compiler flags `/Od /EHsc`.)  
  
See also on [godbolt](https://godbolt.org/z/Ga7sPExPd).  
Note that it does not happen on godbolt because we have only the older boost release available there.  
  
For more detailed information, see the following program (bug and diagnostics included):  
```cpp  
// define a non-aborting assert handler to enable goodl old "cout" debugging  
#define BOOST_ENABLE_ASSERT_HANDLER  
  
#include <boost/container/small_vector.hpp>  
  
using SmallVectorType = boost::container::small_vector<double, 8>;  
  
void diagnose(const SmallVectorType& MyVector);  
  
int main() {  
    SmallVectorType SmallVector;  
    diagnose(SmallVector);  
}  
  
#include <boost/align/alignment_of.hpp>  
#include <cstdint>  
#include <iostream>  
  
void diagnose(const SmallVectorType& MyVector) {  
    std::cout << "Alignment of type (boost): "  
              << boost::alignment::alignment_of<SmallVectorType>() << "\n";  
    std::cout << "Alignment of type (std): "  
              << std::alignment_of<SmallVectorType>() << "\n";  
    std::cout << "Actual alignment: "  
              << reinterpret_cast<std::uintptr_t>(std::addressof(MyVector)) % 8  
              << "\n";  
    std::cout.flush();  
}  
  
namespace boost {  
void assertion_failed(char const* Expression, char const* Function,  
                      char const* File, long Line) {  
    assertion_failed_msg(Expression, nullptr, Function, Function, Line);  
}  
  
void assertion_failed_msg(char const* Expression, char const* Message,  
                          char const* Function, char const* File, long Line) {  
    std::cout << "Assertion failed: " << Expression << " executing function "  
              << Function << " in file " << File << " at line " << Line  
              << ".\n";  
    if (Message) std::cout << Message << "\n";  
    std::cout.flush();  
}  
}  // namespace boost  
```  
See also on [godbolt](https://godbolt.org/z/d68GGfbfe).  
  
This program has one of the two following outputs (one of the dynamically chosen at random):  
  
(1) expected alignment by chance  
> Alignment of type (boost): 8  
Alignment of type (std): 8  
Actual alignment: 0  
  
(2) or the other case  
> Assertion failed: (std::size_t(this) % dtl::alignment_of<strawman_t>::value) == 0 executing function double *__thiscall boost::container::small_vector_allocator<double,class boost::container::new_allocator<void>,void>::internal_storage(void) noexcept in file double *__thiscall boost::container::small_vector_allocator<double,class boost::container::new_allocator<void>,void>::internal_storage(void) noexcept at line 482.  
Alignment of type (boost): 8  
Alignment of type (std): 8  
Actual alignment: 4  
Assertion failed: (std::size_t(this) % dtl::alignment_of<strawman_t>::value) == 0 executing function const double *__thiscall boost::container::small_vector_allocator<double,class boost::container::new_allocator<void>,void>::internal_storage(void) noexcept const in file const double *__thiscall boost::container::small_vector_allocator<double,class boost::container::new_allocator<void>,void>::internal_storage(void) noexcept const at line 460  
  
It would be great to fix that! We make heavy use of `boost::container::small_vector` and also rely on assertions for diagnostics.  
As I have no idea what's going on in this code, I'd be interested to know if it isrecommended to enforce the expected alignment on the stack as a workaround in x86 using `__declspec(align(8))` or simply patch boost by disabling the assertion (in case it is benign).

---

## Comment 3

> Username: rdoeffinger  
> Created at: 2024-01-19 11:27:15 UTC  
> Url: https://github.com/boostorg/container/issues/231#issuecomment-1900235392  

I have no idea how it is supposed to work, not even how the allocator this pointer connects to the storage.  
I guess the part that should be aligned is small_vector_storage_definer<T, N, Options>::type, from which small_vector inherits.  
So at least small_vector static_cast to that should be aligned. Either that is not the case, or I suspect at least for the MSVC debug build, that might not imply that small_vector pointer itself is aligned.

---

## Comment 4

> Username: rdoeffinger  
> Created at: 2024-01-19 12:33:58 UTC  
> Url: https://github.com/boostorg/container/issues/231#issuecomment-1900336233  

Sorry, I missed the information at the start that this is only an issue when allocated on stack.  
The issue should in this case trigger on older Linux 32-bit systems as well, or for data types with really large alignment (> 16 bytes maybe?) even for 64-bit Linux systems.  
I've not tested this though.

---

## Comment 5

> Username: moon6pence  
> Created at: 2024-02-06 15:14:59 UTC  
> Url: https://github.com/boostorg/container/issues/231#issuecomment-1930017561  

In case of stack allocation and memory address is not aligned, is there any potential memory corruption when this assertion is ignored? (well, it's true if there is any issue without assertion that it will also happen in release build..)

---

## Comment 6

> Username: moon6pence  
> Created at: 2024-04-09 14:50:51 UTC  
> Url: https://github.com/boostorg/container/issues/231#issuecomment-2045378054  

Kindly ping this issue. Can we confirm small_vector is doing safe memory access without this assertion?  
  
Otherwise do we need different approach for `small_vector_allocator<>` in 32bit systems?

---

## Comment 7

> Username: igaztanaga  
> Created at: 2024-07-07 18:03:11 UTC  
> Url: https://github.com/boostorg/container/issues/231#issuecomment-2212525037  

Thanks for the report.  
  
This is duplicate of https://github.com/boostorg/container/issues/266, which hopefully will be fixed for soon to be released Boost 1.86.
