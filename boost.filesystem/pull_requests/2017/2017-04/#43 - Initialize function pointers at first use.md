# #43 Initialize function pointers at first use. [Closed]

> Username: DanielaE  
> Created at: 2017-04-12 09:55:48 UTC  
> Updated at: 2018-01-04 13:47:26 UTC  
> Closed at: 2018-01-04 13:47:26 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43  

The former global static initialization suffers from the unspecified total initialization order after linking. If other statics call functions which require these function pointers to be initialized, these calls may cause null pointer exceptions.  
  
Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Comment 1

> Username: DanielaE  
> Created_at: 2017-04-12 10:02:50 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43#issuecomment-293531529  

I was bitten by this problem because of a static object which uses `temp_directory_path()` very early at program start (as early as possible, long before `main`). This used to work up to version 1.63.0 but started to fail with 1.64.0 with its more elaborate compare logic in operations.cpp. The fix rectifies the problem, i.e. no longer null pointer exceptions because of not-yet-initialized function pointers.

---

## Comment 2

> Username: Dani-Hub  
> Created_at: 2017-04-26 17:19:12 UTC  
> Updated_at: 2017-04-26 17:31:43 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43#issuecomment-297481283  

I agree with the problem, this is basically Boost ticket [12987](https://svn.boost.org/trac/boost/ticket/12987). The solution looks good except that it the function local static is problematic for Visual Studio versions less than 2013 (since it is not thread-safe). My recommendation is (I will test that tomorrow) to make the static keyword conditionally added and to use a normal (non-static) variable instead when e.g. the preprocessor condition  
  
```  
#if (defined(_MSC_VER) && (_MSC_VER < 1900)) || (!defined(_MSC_VER) && (__cplusplus < 201103))  
```  
  
is met.  
  
Hmmh, I just notice that the pull request still keeps the global variable `rtl_equal_unicode_string_api`. I'm currently working on a solution that get rids of *all* global variables.

---

## Comment 3

> Username: DanielaE  
> Created_at: 2017-04-26 17:37:08 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43#issuecomment-297486197  

Yeah, that's true. But we should take into account that even if the local static happens to be initialized concurrently, the initialization is trivial (just function pointers) and with identical values. Nothing bad can happen here.

---

## Comment 4

> Username: Dani-Hub  
> Created_at: 2017-04-26 18:45:27 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43#issuecomment-297504998  

AFAIK, the following can happen: The first thread attempts to initialize the locale static. The second thread sees that the "is initialized" flag of the static is set to true, but still reads the initial null value of the zero-initialized static and proceeds with that incorrect pointer value.

---

## Comment 5

> Username: DanielaE  
> Created_at: 2017-04-27 12:03:40 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43#issuecomment-297694205  

After some mulling over your comment I've reworked the patch a little. The initialization of the function pointers now takes place no later than in the global static initialization phase, but as early as on first use.

---

## Comment 6

> Username: Dani-Hub  
> Created_at: 2017-04-27 13:41:53 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43#issuecomment-297716520  

Thanks, looks much better! I have started testing it against VS 2012 and VS 2017, but that might need a while.

---

## Comment 7

> Username: DanielaE  
> Created_at: 2017-04-27 14:20:50 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43#issuecomment-297727803  

VS2017 is definitely fine - that's my primary test compiler (with options /std:c++latest /permissive-). And so are VS2015, VS2013, VS2012 and VS2010. Later compilers are free of warnings at /W4 if [PR#44](https://github.com/boostorg/filesystem/pull/44) is applied, too.

---

## Comment 8

> Username: Dani-Hub  
> Created_at: 2017-04-28 19:53:11 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43#issuecomment-298091585  

The results for VS 2012 and VS 2017 look promising and I didn't stumble across problems yet, I'm now extending my tests to MinGW and will report about the outcome. Nonetheless I'm bit nervous regarding the possibility that a compiler might remove the definition of `compare_function_initializer` and its side-effects. This may happen for not very conforming compilers (E.g. a possible candidate might be VS 2008, but untested) and the fact that there are real-world library constellations possible that are not well specified by the standard. In this case in a multi-threading program we have a data race because of the unprotected pointer. To be fair, I couldn't yet see any problems with such a situation, because this scenario can already now be emulated by provoking multi-threading in a static initializer such as the following program so that `compare_function_initializer` becomes initialized *after* the first call of `equal_string_ordinal_ic` as shown by the following test case  
  
```  
#include <vector>  
#include <boost/filesystem.hpp>  
#include <boost/thread.hpp>  
  
namespace fs = boost::filesystem;  
  
struct StaticInitializer {  
  StaticInitializer() {  
    static const auto path = fs::current_path();  
    auto func = [] {  
      fs::status(path);  
    };  
    std::vector<boost::thread> ths{};  
    const auto max_count = 10;  
    for (int i = 1; i <= max_count; ++i) {  
      ths.emplace(ths.begin(), func);  
    }  
    for (auto& th : ths) {  
      th.join();  
    }  
  }  
} instance;  
  
int main()  
{  
}  
```

---

## Comment 9

> Username: Dani-Hub  
> Created_at: 2017-04-28 21:22:25 UTC  
> Updated_at: 2017-04-29 14:35:47 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43#issuecomment-298109801  

The code also runs successfully with MinGW using gcc 8.0 HEAD. For practical purposes your patch looks good to me.

---

## Comment 10

> Username: DanielaE  
> Created_at: 2017-04-29 14:19:15 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43#issuecomment-298171761  

Challenge accepted :smile:  
 I've added the VS2008 toolset to my compiler zoo and ran the unit testsuite of Boost.Filesystem. Everything is ok there as expected. Then I transformed your test code from above back into an equivalent stone-age variant  
  
```  
#include <boost/filesystem.hpp>  
#include <boost/thread.hpp>  
  
namespace fs = boost::filesystem;  
  
struct StaticInitializer {  
  StaticInitializer() {  
    static const fs::path path = fs::current_path();  
    struct {  
      void operator()() { fs::status(path); }  
    } func;  
    const int max_count = 10;  
    boost::thread ths[max_count];  
    for (int i = 0; i < max_count; ++i) {  
      new (&ths[i]) boost::thread(func);  
    }  
    for (int i = 0; i < max_count; ++i) {  
      ths[i].join();  
    }  
  }  
} instance;  
  
int main()  
{  
}  
```  
compiled and ran it with VS2017 and VS2008. The outcome is exactly the same with both compilers:  
  
- works with my patch applied  
- crashes because of an null pointer exception without my patch

---

## Comment 11

> Username: Dani-Hub  
> Created_at: 2017-04-29 14:37:55 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43#issuecomment-298172859  

Thanks very much for your additional thorough testing, Daniela! I would strongly vote for your patch to become accepted.

---

## Comment 12

> Username: DanielaE  
> Created_at: 2017-04-29 15:39:43 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43#issuecomment-298176309  

Thanks, Daniel,  for your kind referral!

---

## Comment 13

> Username: Klaim  
> Created_at: 2017-05-08 19:28:00 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43#issuecomment-299966276  

I just upgraded from Boost 1.61.0 to Boost 1.64.0 and switched from vs2015  to vs2017 and hit the exact same issue.  
Except making all my constants static local object in functyions, I can't think of a workaround.

---

## Comment 14

> Username: gyokketto  
> Created_at: 2017-05-09 16:51:26 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43#issuecomment-300229097  

@DanielaE. I think this is a graet solution but your previous solution was better. I think static objects have to be moved into functions. I know in this particular case assigning/accessing a function pointer is thread-safe (in practice at least), but in general we should rely on the local static initialisation thread-safety guarantee or in pre-c++11 we should implement it. See this more complicated but similar issue: [https://svn.boost.org/trac/boost/ticket/6320](https://svn.boost.org/trac/boost/ticket/6320)

---

## Comment 15

> Username: Dani-Hub  
> Created_at: 2017-05-09 18:54:08 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43#issuecomment-300266733  

I disagree with the statement that the previous solution was better. In fact, it was problematic because of the missing thread-safe static function local initialization of Visual Studio Compilers below VS 2015. Would your concerns be satisfied, if an atomic function pointer would be used?

---

## Comment 16

> Username: DanielaE  
> Created_at: 2017-05-10 06:13:43 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43#issuecomment-300385224  

While looking at the assembly of thread-safe 'Meyer's singletons' a.k.a 'magic statics' I noticed their associated costs. I'd rather strive for an as-cheap-as-possible atomic solution instead if that's the concern. Simply put, I wanted to avoid a new dependency on yet another Boost library.

---

## Comment 17

> Username: gyokketto  
> Created_at: 2017-05-10 11:20:02 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43#issuecomment-300452234  

We can use a non-local static before it is dynamic-initialised:  
  
path.cpp (filesystem.lib):  
```  
namespace  
{  
	int x = [] { return 42; }();  
}  
  
//global  
void x_set(int i)  
{  
	x = i;  
}  
  
int x_get()  
{  
	return x;  
}  
  
  
```  
main.cpp:  
```  
#include <boost/filesystem.hpp>  
  
void x_set(int);  
int x_get();  
  
int dummy = [] { std::cout << x_get() << std::endl; x_set(43); std::cout << x_get() << std::endl; return 0; }();  
  
int main(int argc, char* argv[])  
{  
	std::cout << x_get() << std::endl;  
	x_set(44);  
	std::cout << x_get() << std::endl;  
  
	return 0;  
}  
```  
  
Output (x64, vc14):  
```  
0  
43  
42  
44  
```  
  
In general if no compelling reason we should avoid non-local statics.  
  
Here, actually, the performance penalty and library dependency is a good argument.

---

## Comment 18

> Username: DanielaE  
> Created_at: 2017-05-10 11:49:47 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43#issuecomment-300458283  

And now check with msvc 9.0 ...

---

## Comment 19

> Username: Dani-Hub  
> Created_at: 2017-05-17 20:14:18 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43#issuecomment-302218543  

To make progress here, I would like to make a suggestion that doesn't require a dependency to another library (like Boost Atomic): Since the code we are looking at is completely Windows specific we could realize a very cheap atomic pointer initialization by means of `BOOST_INTERLOCKED_` functionality. A very similar problem and a thread-safe solution via Windows interlocked functions can be found in  
`<boost/interprocess/detail/win32_api.hpp>` line 1562 or line 1590 within class template `function_address_holder`. With this idiom Daniela's last pull request could be improved by slightly adjusting the current `compare()` function to use this interlocked-spinlock approach. To get access to the interlocked functions just include `<boost/detail/interlocked.hpp>`, ending in something like the following:  
```  
 static long compare_function_state;  
   
 inline Ptr_equal_string_ordinal_ic compare()  
 {  
   for (unsigned i = 0; compare_function_state < 2; ++i) {  
     if (BOOST_INTERLOCKED_COMPARE_EXCHANGE(&compare_function_state, 1, 0) == 0){  
       compare_function = get_compare_function();  
       BOOST_INTERLOCKED_INCREMENT(&compare_function_state);  
       break;  
     }  
     else if (i & 1) {  
       sched_yield(); // Same as in win32_api.hpp  
     }  
     else {  
       sleep_tick(); // Same as in win32_api.hpp  
     }  
   }  
   return compare_function;  
 }  
```  
What do you think?

---

## Comment 20

> Username: DanielaE  
> Created_at: 2017-05-18 15:25:58 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43#issuecomment-302439473  

Hi Daniel!  
  
IMHO, this doesn't make sense at all because it doesn't gain you anything but a highly convoluted and slow code in the hot path. Look:  
  
1. `get_compare_function` is pure. It has no side effects and returns the very same value on every call.  
2. the static function pointer `compare_function` has exactly two possible states and exactly one transition from its initial value `nullptr` to the return value of `get_compare_function`. This transition to its final constant value takes place at the very first successful store to `compare_function` - independently of the number of  cpu cores attempting this transition in parallel. The only possibly noticable side effect is a slight execution delay because of transitions in the MO(E)SI cache coherency protocol if more than one core is triggering the store to the function pointer at the very same instant of time.  
  
The equivalent implementation using c++ atomic goes along this line (comp1/2 for exposition only):  
```  
#include <atomic>  
using namespace std;  
  
using cfun = bool(*)(const char*, const char*);  
  
static bool comp1(const char* a, const char* b) { return a != b; }  
static bool comp2(const char* a, const char* b) { return a == b; }  
  
volatile bool choice;  
volatile __attribute__ ((noinline)) cfun which_cfun() {  
    return choice ? comp1 : comp2;  
}  
  
static atomic<cfun> pfun;  
  
inline cfun fun() {  
    cfun f;  
    if (!(f = pfun.load(memory_order_consume))) {  
        pfun.store(f = which_cfun(), memory_order_release);  
    }  
    return f;  
}  
  
bool compare(const char* a, const char* b) {  
    return fun()(a, b);  
}  
```  
put this into Compiler Explorer and watch the generated assembly for Intel or recent ARM (or put it into Visual Studio and look at the generated assembly code):  
Intel  
```  
which_cfun():                        # @which_cfun()  
        cmp     byte ptr [rip + choice], 0  
        mov     ecx, comp1(char const*, char const*)  
        mov     eax, comp2(char const*, char const*)  
        cmovne  rax, rcx  
        ret  
  
comp1(char const*, char const*):                        # @comp1(char const*, char const*)  
        cmp     rdi, rsi  
        setne   al  
        ret  
  
comp2(char const*, char const*):                        # @comp2(char const*, char const*)  
        cmp     rdi, rsi  
        sete    al  
        ret  
  
compare(char const*, char const*):                       # @compare(char const*, char const*)  
        push    r14  
        push    rbx  
        push    rax  
        mov     r14, rsi  
        mov     rbx, rdi  
        mov     rax, qword ptr [rip + pfun]  
        test    rax, rax  
        jne     .LBB3_2  
        call    which_cfun()  
        mov     qword ptr [rip + pfun], rax  
.LBB3_2:  
        mov     rdi, rbx  
        mov     rsi, r14  
        add     rsp, 8  
        pop     rbx  
        pop     r14  
        jmp     rax                     # TAILCALL  
```  
ARM  
```  
comp1(char const*, char const*):  
        cmp     x1, x0  
        cset    w0, ne  
        ret  
comp2(char const*, char const*):  
        cmp     x1, x0  
        cset    w0, eq  
        ret  
which_cfun():  
        adrp    x0, .LANCHOR0  
        ldrb    w0, [x0, #:lo12:.LANCHOR0]  
        uxtb    w0, w0  
        cbnz    w0, .L5  
        adrp    x0, comp2(char const*, char const*)  
        add     x0, x0, :lo12:comp2(char const*, char const*)  
        ret  
.L5:  
        adrp    x0, comp1(char const*, char const*)  
        add     x0, x0, :lo12:comp1(char const*, char const*)  
        ret  
compare(char const*, char const*):  
        adrp    x2, .LANCHOR0  
        mov     x4, x0  
        add     x2, x2, :lo12:.LANCHOR0  
        add     x0, x2, 8  
        ldar    x3, [x0]  
        cbz     x3, .L11  
        mov     x0, x4  
        br      x3  
.L11:  
        stp     x29, x30, [sp, -16]!  
        add     x29, sp, 0  
        bl      which_cfun()  
        mov     x3, x0  
        add     x0, x2, 8  
        stlr    x3, [x0]  
        ldp     x29, x30, [sp], 16  
        mov     x0, x4  
        br      x3  
```  
It looks like magic but the atomic has completely vanished. The reason for that lies in the function pointer! The atomic load, test, and indirect call establishes a 'carries-dependency' relationship which requires no atomic in the first place because this dependency is built right into all cpu types (besides DEC Alpha)! No further synchronization required, no slow execution paths, none of that. The Linux kernel relies heavily on this behaviour thereby avoiding any heavy synchronization primitives or even slow code paths because of core synchronization due to atomics or memory barriers.

---

## Comment 21

> Username: Lastique  
> Created_at: 2017-08-08 18:19:09 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43#issuecomment-321039396  

> the static function pointer `compare_function` has exactly two possible states and exactly one transition from its initial value `nullptr` to the return value of `get_compare_function`.  
  
C++ does not guarantee that - neither store atomicity, nor visibility to other threads. Visibility might not be a big problem if the store is atomic (other threads may repeat runtime function resolution and end up with the same result) but store atomicity can be a problem. I know you can rely on the compiler and the underlying hardware architecture doing what you intend to do for you, but that doesn't make the code correct.  
  
> The atomic load, test, and indirect call establishes a 'carries-dependency' relationship which requires no atomic in the first place because this dependency is built right into all cpu types (besides DEC Alpha)!  
  
Compilers are allowed to transform data dependency into control dependency by inserting dummy branches to leverage OoO execution in the CPU. This ruins dependency tracking and makes the code thread-unsafe. See this excellent post for more details: http://lists.boost.org/Archives/boost/2014/06/213890.php. Linux kernel gets by by constantly battling the compiler by disabling offending optimizations and using assembly where needed. Besides, Boost has a much larger set of supported compilers, some of which we don't even know or test regularly.  
  
Bottom line: forget about `memory_order_consume` and `carries_dependency` unless you're writing very specific code where resorting to assembler is not something out of line.

---

## Comment 22

> Username: k15tfu  
> Created_at: 2017-10-03 09:40:08 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43#issuecomment-333791553  

Guys, does anyone have any plans to merge this? I'm stuck with https://svn.boost.org/trac10/ticket/13024 .

---

## Comment 23

> Username: Dani-Hub  
> Created_at: 2017-11-18 21:52:14 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43#issuecomment-345474047  

I'm now in the process to prepare a pull request that borrows most of Daniela's ideas and combines it with `BOOST_INTERLOCKED_*` functionality as described in https://github.com/boostorg/filesystem/pull/43#issuecomment-302218543. Expect this being ready not later than Monday of the upcoming week.

---

## Comment 24

> Username: DanielaE  
> Created_at: 2017-11-19 07:58:14 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43#issuecomment-345498911  

I don't see what `BOOST_INTERLOCKED_*` buys you other than taking a dependency on a different Boost library but Boost.Atomic (or `std::atomic`). There is only one variable that needs relaxed atomic treatment (the compare function pointer) - either by a library or implicitly by the processor. In it's latest incarnation, the code is carefully laid out such that it doesn't matter if you use an atomic function pointer with relaxed semantics or a non-atomic one: the compiler sees through the layers of abstractions and compiles to the very same instruction stream (checked with Compiler Exporer). Even in face of speculation, instruction reordering or scheduling, nothing can destroy the semantics of this code. In addition to that, the compiler still has all options available to inline and optimize the rapid succession of calls to this code.  
  
I'd rather see a comment from the maintainer on how to proceed with this issue: take an additional depency on Boost.Atomic or rather go with naked C which takes advantage of the built-in memory ordering guarantees of the processors in question (x86 and ARM only!). Both implementations are available in the pull request.

---

## Comment 25

> Username: Dani-Hub  
> Created_at: 2017-11-19 10:11:46 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43#issuecomment-345505585  

What do you mean by "Both implementations are available in the pull request"?

---

## Comment 26

> Username: DanielaE  
> Created_at: 2017-11-19 10:32:51 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43#issuecomment-345506769  

Hi Daniel,  in https://github.com/boostorg/filesystem/pull/43/commits/ff8c321a655490783c7aac589796a5ee35a749e1 the implementation based on atomics is commented out (lines 625 ... 638), the other one implemented in plain C++98 is active. Both implementations are eqivalent after lowering by the compiler.

---

## Comment 27

> Username: Dani-Hub  
> Created_at: 2017-11-19 10:43:08 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43#issuecomment-345507314  

Thanks for clarification, Daniela. Your comment was a bit misleading because I interpreted it as referring to being part of the real code. My motivation to provide an alternative P/R was based on the seemingly hesitation to accept the P/R, not because I don't agree with your pull request. I had already expressed my agreement with your fix in two comments and I can report that we (in our company) are working with the patch provided by Daniela successfully since June this year. Please let us make some progress here. If the pull request is not accepted, please provide more concrete information about the preferred resolution.

---

## Comment 28

> Username: Lastique  
> Created_at: 2017-11-19 11:06:25 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43#issuecomment-345508401  

> In it's latest incarnation, the code is carefully laid out such that it doesn't matter if you use an atomic function pointer with relaxed semantics or a non-atomic one: the compiler sees through the layers of abstractions and compiles to the very same instruction stream (checked with Compiler Exporer). Even in face of speculation, instruction reordering or scheduling, nothing can destroy the semantics of this code.  
  
I repeat, the code does not guarantee atomicity. For example, the compiler is allowed to load/store the pointer in multiple parts. Or CPU is. The compiler also does not generate any special instructions that may be required by the CPU to enforce atomicity. That you checked in Compiler Explorer on one compiler for one target hardware doesn't mean anything.

---

## Comment 29

> Username: DanielaE  
> Created_at: 2017-11-19 13:54:11 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43#issuecomment-345518584  

Andrey, please stop arguing this way. Analyse the code and then come back. Are you really telling us that your library Boost.Atomic doesn't work at all? Both the compiler and the cpu are allowed to reorder and speculate as much as it doesn't violate the contract but no more. The code does rely on implict data dependencies only which every cpu on earth does obey unless it enters the realm of probabilistic computing (very rare today but interesting anyways). You may switch compilers and target cpus, Compiler Explorer gives you the same answers - because of that! And if the maintainer of Boost.Filesystem decides to prefer using Boost.Atomic I'm perfectly happy with such a decision because ist doesn't change a bit in the outcome. In fact, I'm in favour of doing so - even if it is only to stop this pointless arguing.

---

## Comment 30

> Username: Lastique  
> Created_at: 2017-11-19 14:22:06 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43#issuecomment-345520339  

I did look at the code and Boost.Atomic is irrelevant wrt. my point. I'm saying that the code, according to the C++ standard, is UB because it contains a data race. I've given an example of how it may manifest in practice (partial stores or loads and load/store combining are not uncommon, although probably not as likely to happen this particular code). Compiler Explorer or any other experiments are irrelevant and don't change that. It just proves that *some particular compilers* implement this UB the way you like in the given use case. It may change at any point, including with the very same compilers you tested but in other circumstances (e.g. by enabling WPO the compiler may be able to group data members more efficiently and use load/store merging). Relying on this behavior is not portable, and IMHO not acceptable.  
  
PS: Boost.Atomic works because it either uses compiler intrinsics that the compiler implements as atomic instructions or uses inline assembler to emit such instructions. Regular loads and stores do not provide atomic guarantees - the compiler is basically free to arrange memory accesses as it sees fit, including omitting them entirely. This is why we use a mutex-backed implementation in Boost.Atomic when none of the above is available.  
  
PPS: Boost.Atomic is not my library, I'm just a maintainer.

---

## Comment 31

> Username: Dani-Hub  
> Created_at: 2017-11-20 19:44:48 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43#issuecomment-345807805  

To make progress here I have added an alternative pull request that differs by that from Daniela by using interlocked functions and adding two test cases. Tested using VS 2012 and VS 2017, see https://github.com/boostorg/filesystem/pull/59.

---

## Comment 32

> Username: pdimov  
> Created_at: 2017-11-21 23:53:23 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43#issuecomment-346199180  

Are we concerned here with the corner case of threads started before `main`?

---

## Comment 33

> Username: DanielaE  
> Created_at: 2017-11-22 06:27:24 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43#issuecomment-346255677  

Peter, exactly this use-case. Personally I am perfectly fine with activating the boost::/std::atomic code path, but I want to leave the decision of taking on a dependency on another Boost library to higher Boost powers 👼 . In our production code we are perfectly fine without doing so.

---

## Comment 34

> Username: pdimov  
> Created_at: 2017-11-22 12:56:35 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43#issuecomment-346342458  

But as far as I can see, the actual problems caused by this issue are more mundane; spawning threads before `main` is rare and there are no bug reports with similar scenarios.

---

## Comment 35

> Username: DanielaE  
> Created_at: 2017-11-22 14:16:24 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43#issuecomment-346362229  

Right. Therefore I've structured the code such that it is fine both in the typical single-threaded use case and the rare (possibly non-existent) use-case of multithreaded dynamic initialization by relying on guaranteed processor execution semantics only.

---

## Comment 36

> Username: pdimov  
> Created_at: 2017-11-22 14:50:16 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43#issuecomment-346371908  

I can't help but notice that the only use of `equal_string_ordinal_ic` is:  
  
```  
    if (equal_string_ordinal_ic(ext.c_str(), L".exe")  
      || equal_string_ordinal_ic(ext.c_str(), L".com")  
      || equal_string_ordinal_ic(ext.c_str(), L".bat")  
      || equal_string_ordinal_ic(ext.c_str(), L".cmd"))  
```  
  
and since the comparison is against simple ASCII, all the effort in achieving correct Unicode case-insensitive comparison is effectively wasted. A simple `_wcsicmp` would work fine here, even under the C locale.

---

## Comment 37

> Username: Dani-Hub  
> Created_at: 2017-11-22 14:57:52 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43#issuecomment-346374343  

This was specifically added by https://github.com/boostorg/filesystem/pull/41 to allow in the future case-correct filename comparison.

---

## Comment 38

> Username: pdimov  
> Created_at: 2017-11-22 15:15:25 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43#issuecomment-346380074  

What are the scenarios in which this future case-correct filename comparison is expected to occur? Because for this specific problem, #41 seems massively overengineered, even if we ignore the initialization problems. To eliminate the unnecessary narrow conversion, we only needed to change `_stricmp` to `_wcsicmp`.

---

## Comment 39

> Username: DanielaE  
> Created_at: 2017-11-22 15:26:22 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43#issuecomment-346383348  

IIUC, Daniel opened PR https://github.com/boostorg/filesystem/pull/41 because of performance issues with _wcsicmp.

---

## Comment 40

> Username: pdimov  
> Created_at: 2017-11-22 15:42:43 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43#issuecomment-346388433  

I see now that his initial suggestion (first commit) in #41 was exactly `_wcsicmp`.

---

## Comment 41

> Username: pdimov  
> Created_at: 2017-11-22 17:25:38 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43#issuecomment-346419753  

On one hand, I understand that the general case-insensitive comparison introduced in #41 can be valuable and that it represents quite a bit of effort and research, so I was reluctant to go back. On the other, the specific case we have here does not need a general case-insensitive comparison and a simple, ad-hoc one, will do. Hence #62.

---

## Comment 42

> Username: Klaim  
> Created_at: 2017-12-23 18:48:57 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43#issuecomment-353742063  

We're at 1.66.0 and I still have to move that variable as static inside the function to remove my systematic startup crash. I don't understand all the details of this problem (the discussion goes into tech details I don't understand) but I would like to know if there is a consensus solution emerging? It is not clear from just reading the discussion.

---

## Comment 43

> Username: Dani-Hub  
> Created_at: 2017-12-23 18:53:09 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43#issuecomment-353742267  

Personally I believe that among Daniela, Peter, and me there exists agreement that Peter's pull request https://github.com/boostorg/filesystem/pull/62 looks like the best way to solve the problem, but it needs to await approval by Beman.

---

## Comment 44

> Username: pdimov  
> Created_at: 2017-12-23 19:23:10 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43#issuecomment-353743663  

I'm going to merge #62 in a few days if we don't hear from @Beman.

---

## Comment 45

> Username: pdimov  
> Created_at: 2018-01-04 13:47:26 UTC  
> Url: https://github.com/boostorg/filesystem/pull/43#issuecomment-355286037  

Merged #62.

---
