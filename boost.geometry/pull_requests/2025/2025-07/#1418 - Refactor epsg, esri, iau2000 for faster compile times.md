# #1418 Refactor epsg, esri, iau2000 for faster compile times. [Merged]

> Username: tinko92  
> Created at: 2025-07-21 16:34:55 UTC  
> Updated at: 2025-08-04 08:22:03 UTC  
> Merged at: 2025-08-04 08:22:03 UTC  
> Closed at: 2025-08-04 08:22:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/1418  

This is another proposal to reduce the compile times of epsg.hpp, esri.hpp, and iau2000.hpp. Since #1214  was rejected for added complexity, this does not include perfect hashing and leaves the tables in a more readable and more easily editable state.  
  
It reduces the compile time under gcc for the following program from about 7 minutes with 1.5GB memory usage, which seems unreasonable, to under 4 seconds with half of that memory use.  
  
```  
#include <boost/geometry.hpp>  
#include <boost/geometry/srs/epsg.hpp>  
#include <boost/geometry/srs/projections/epsg_params.hpp>  
  
namespace bg = boost::geometry;  
  
int main() {  
    auto epsg2000 = bg::projections::dynamic_parameters<bg::srs::epsg>::apply(bg::srs::epsg{2000});  
    auto epsg2001 = bg::projections::dynamic_parameters<bg::srs::epsg>::apply(bg::srs::epsg{2001});  
    return epsg2000.size();  
}  
```  
  
With these changes, rather than generating thousands of assembly instructions to build the vectors at initialisation, the static data is put into the rodata section of the binary, which seems more appropriate for static data, and the vector is only build at retrieval for the code that is actually accessed.  
  
This PR still results in a large binary, as does the current develop code. This could be solved with a changed, constexpr dpar::parameters, which would require two more helper classes, a constexpr static vector (could be replaced by std::inplace_vector when supports is bumped to C++26 in the far future) and a constexpr variant (could be replaced by std::variant when support is bumped to C++17 in the future). For compile-time constant codes, GCC and Clang could then recognise the opportunity for const propagation and not need store the entire parameter table in the binary at all (see e.g. https://godbolt.org/z/5reM6e3zf). But this idea is not included in the PR because it would change the interface and add two more classes (not too complicated ones but also not trivial).

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2025-07-23 12:43:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/1418#issuecomment-3107928323  

Great, thanks. This keeps indeed everything readable and will be acceptable for me. The differences are not rendered in this PR but I downloaded the PR as a patch, compared, and it looks OK to me. I will approve it.  
  
I'm just curious to try it. I never have these long compilation times. Maybe this part is not in CMake yet. Which source should I compile to notice the differences?

---

## Comment 2

> Username: tinko92  
> Created_at: 2025-07-24 03:08:24 UTC  
> Updated_at: 2025-07-24 08:35:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/1418#issuecomment-3111800964  

> I'm just curious to try it. I never have these long compilation times. Maybe this part is not in CMake yet. Which source should I compile to notice the differences?  
  
I attached a minimal test case (file suffix .cpp.txt due to github limitations).  
  
[epsg2000_size.cpp.txt](https://github.com/user-attachments/files/21398711/epsg2000_size.cpp.txt)  
  
Content:  
```cpp  
#include <boost/geometry.hpp>  
#include <boost/geometry/srs/epsg.hpp>  
#include <boost/geometry/srs/projections/epsg_params.hpp>  
  
namespace bg = boost::geometry;  
  
int main() {  
    auto epsg2000 = bg::projections::dynamic_parameters<bg::srs::epsg>::apply(bg::srs::epsg{2000});  
    return epsg2000.size();  
}  
```  
  
The compile times depend on compiler version and flags. I do not think they depend much on the arch and system, but for the record, I tested this on WSL Ubuntu 25.04 aarch64, Snapdragon X Elite CPU.  
  
Compiling this with different compiler versions and flags and with `export BOOST_REPO=/path/to/my/boost/folder` in the following loop  
  
```bash  
for flags in "" "-O2"; do  
    for compiler in g++-11 g++-12 g++-13 g++-14 g++-15 clang++-14 clang++-15 clang++-17 clang++-18 clang++-19 clang++-20; do  
        echo $compiler $flags && {/usr/bin/time -f "%e" $compiler -I$BOOST_REPO epsg2000_size.cpp $flags -o $compiler-out-$flags -S;} 2> $compiler-$flags-time ;  
    done;  
done  
```  
So the compile command is g++-11 -I/path/to/my/boost/folder epsg2000_size.cpp and so on.   
  
I get the following durations for compilations in seconds:  
| Compiler | develop | develop ‑O2 | fix/srs‑compile‑times | fix/srs‑compile‑times ‑O2 |  
|---|---|---|---|---|  
| g++-11 | 7.77 | 120.16 | 4.06 | 3.79 |  
| g++-12 | 540.69 | 1518.27 | 4.05 | 4.13 |  
| g++-13 | 430.64 | 2624.35 | 3.71 | 3.58 |  
| g++-14 | 951.42 | 4044.91 | 3.80 | 5.78 |  
| g++-15 | 75.79 | 786.81 | 2.81 | 3.11 |  
| clang++-14 | 6.00 | 8.87 Command terminated by signal 11 | 3.95 | 4.16 |  
| clang++-15 | 5.09 | 7.85 Command terminated by signal 11 | 6.16 | 3.77 |  
| clang++-17 | 5.59 | 63.41 | 3.74 | 3.73 |  
| clang++-18 | 5.89 | 77.20 | 3.99 | 3.86 |  
| clang++-19 | 6.24 | 68.63 | 3.96 | 4.07 |  
| clang++-20 | 8.68 | 74.93 | 4.16 | 4.08 |  
  
So, in the worst case (g++-14 -O2) on develop for this test, it took more than an hour, as measured by GNU time.  
  
I did not systematically record RAM usage, but the highest I saw for current develop was 8.5GB (no swapping, though, so this wasn't the reason for the slowdown) with g++-14 -O2, and for fix/srs-compile-times I saw 1GB RAM usage for the same setting.  
  
Develop and this PR lead to very different code generation. With g++-14 and -O2, for develop I get assembly that has tens of thousands of lines that look like this:  
  
```asm  
.LEHB4206:  
        bl      _ZNSt6vectorIN5boost8geometry3srs4dpar9parameterIdEESaIS5_EE12emplace_backIJRNS3_6name_fEiEEERS5_DpOT_.isra.0  
.LEHE4206:  
        add     x1, sp, 16384  
        mov     w0, 41248  
        movk    w0, 0x7, lsl 16  
        mov     x11, 28200  
        mov     x12, 28204  
        add     x2, sp, x11  
        str     w0, [x1, 11816]  
        mov     w0, 20  
        str     w0, [x1, 11820]  
        add     x0, sp, 172032  
        add     x1, sp, x12  
        add     x0, x0, 1080  
.LEHB4207:  
        bl      _ZNSt6vectorIN5boost8geometry3srs4dpar9parameterIdEESaIS5_EE12emplace_backIJRNS3_6name_fEiEEERS5_DpOT_.isra.0  
.LEHE4207:  
```  
  
so it directly generates the assembly for all of those vector operations and the static data is intermingled with the code. The binary size for the appended test program with g++-15 -O2 is 2.8M on develop and it takes ~6ms to run (I suspect due to the thousands of calls to the allocator for each of the vectors in dpar::parameters at the initial static array initialisation).  
  
With the branch fix/srs-compile-times, the content of arr itself is stored in a very long read-only data section in the binary  
```asm  
_ZZN5boost8geometry11projections6detail18epsg_to_parametersEiE3arr:  
        .word   2000  
        .zero   4  
        .word   82  
        .zero   4  
        .word   124  
        .zero   4  
        .word   1  
        .zero   4  
        .word   25  
        .zero   4  
        .word   0  
        .word   0  
        .word   2  
        .zero   4  
        .word   31  
        .zero   4  
        .word   0  
        .word   -1068564480  
```  
And the vectors for a specific dpar::parameters<> instance are only build when such an instance is returned from the method. The binary size for the appended test program with g++-15 -O2 is 1.5M on fix/srs-compile-times and it takes ~2ms to run.  
  
For clang, though, on develop I get a 1.3M (due to less inlining) binary and a 1.5M binary on my branch. The fundamental difference in the kind of code generated (static data mixed with instructions on develop vs. long read only data section) is still the same but clang does the former more space efficiently than gcc apparently. The patched code still runs a little faster.

---

## Review 3 [Approved]

> Username: vissarion  
> Created_at: 2025-07-24 09:39:10 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1418#pullrequestreview-3050900834  

Thanks, Tinko for this PR and for the timings. It looks good to me.

---

## Comment 4

> Username: tinko92  
> Created_at: 2025-07-24 10:40:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/1418#issuecomment-3112966067  

Thanks, Vissarion and Barend, for the quick feedback. Since this would not be reasonable to check for mistakes manually, the following test programs https://gist.github.com/tinko92/e75471ecf4f6eabc7117fd75308b8aaa produced no output for me on this test branch which includes old and new and (for testing this PR only) modifications to allow equality comparison of parameters: https://github.com/tinko92/geometry/tree/test-srs-projections-comparison .

---

## Review 5 [Approved]

> Username: vissarion  
> Created_at: 2025-07-24 10:48:19 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1418#pullrequestreview-3051132802  

I took another look and I have a minor comment before merging.

📁 include/boost/geometry/srs/projections/code.hpp

```diff
  93 |+             for (const auto& p : parameters)
  94 |+             {
  95 |+                 if(towgs84_to_be_inserted && (p.id == srs::dpar::units || p.id == srs::dpar::no_defs))
```

> Username: vissarion  
> Created_at: 2025-07-24 10:47:51 UTC  
> Updated_at: 2025-07-24 10:48:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/1418#discussion_r2228162963  

There is a space missing between `if` and `(`. Same to the if statement in line 101.

> Username: tinko92  
> Created_at: 2025-07-24 11:30:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/1418#discussion_r2228250754  

Thanks, both addressed in latest force push.


---

## Review 6 [Approved]

> Username: barendgehrels  
> Created_at: 2025-07-24 15:58:40 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1418#pullrequestreview-3052280514  

Thanks for the revised PR and the explanations!

---

## Comment 7

> Username: barendgehrels  
> Created_at: 2025-07-24 16:03:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/1418#issuecomment-3114028782  

Minor comment: in two files:  
```  
        const code_element * first = arr.begin();  
        const code_element * last = arr.end();  
```  
in `iau2000.hpp` you used `cbegin()` and `cend()`. You might harmonize it.

---

## Comment 8

> Username: tinko92  
> Created_at: 2025-07-24 23:45:42 UTC  
> Updated_at: 2025-07-25 00:54:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/1418#issuecomment-3115342511  

> in `iau2000.hpp` you used `cbegin()` and `cend()`. You might harmonize it.  
  
Thanks, I missed that. I changed it to begin() and end() to be consistent with the rest of the library and noted that the code can be simplified by calling std::lower_bound directly.

---

## Comment 9

> Username: barendgehrels  
> Created_at: 2025-08-01 16:38:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/1418#issuecomment-3145152554  

@tinko92 can we merge it?

---

## Comment 10

> Username: tinko92  
> Created_at: 2025-08-02 05:40:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/1418#issuecomment-3146243787  

> @tinko92 can we merge it?  
  
@barendgehrels Yes. As a general rule shall I merge my PRs when they pass all reviews like this one or is that for the maintainers only?

---

## Comment 11

> Username: barendgehrels  
> Created_at: 2025-08-04 05:55:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/1418#issuecomment-3149220653  

> > @tinko92 can we merge it?  
>   
> @barendgehrels Yes. As a general rule shall I merge my PRs when they pass all reviews like this one or is that for the maintainers only?  
  
Good question. We've no strict rules. For a bit larger PR like this, I would say that you can merge it if there are two approvals and not remaining questions. For a smaller and simpler one like a one-liner (your previous), one approval is enough.

---
