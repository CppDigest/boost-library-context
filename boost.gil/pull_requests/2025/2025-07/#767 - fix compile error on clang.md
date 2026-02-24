# #767 fix compile error on clang [Merged]

> Username: shyeyian  
> Created at: 2025-07-02 17:17:08 UTC  
> Updated at: 2025-07-11 07:27:34 UTC  
> Merged at: 2025-07-03 12:53:21 UTC  
> Closed at: 2025-07-03 12:53:21 UTC  
> Url: https://github.com/boostorg/gil/pull/767  

Reopen pull request #766.  
  
Source:  
```cpp  
#include <boost/gil.hpp>  
```  
  
Compilation (clang++ 20.1.7)  
```text  
clang++ -std=c++26 ./test.cpp -o test  
./bin/debug/third_party/install/include/boost/gil/algorithm.hpp:1241:54: error: a template argument list is expected after a name prefixed by the template keyword [-Wmissing-template-arg-list-after-template-kw]  
 1241 |         return inner_product_k_t<Size - 1>::template apply(  
      |                                                      ^  
./bin/debug/third_party/install/include/boost/gil/algorithm.hpp:1288:54: error: a template argument list is expected after a name prefixed by the template keyword [-Wmissing-template-arg-list-after-template-kw]  
 1288 |     return detail::inner_product_k_t<Size>::template apply(  
      |                                                      ^  
2 errors generated.  
```  
  
File changed:  
- Remove these 2 `template`.  
  
**Thank you.**

---

## Comment 1

> Username: mloskot  
> Created_at: 2025-07-02 20:04:49 UTC  
> Url: https://github.com/boostorg/gil/pull/767#issuecomment-3029162794  

I wonder what is the blast radius of this fix w.r.t. older compilers

---

## Comment 2

> Username: shyeyian  
> Created_at: 2025-07-03 06:54:51 UTC  
> Updated_at: 2025-07-03 12:12:36 UTC  
> Url: https://github.com/boostorg/gil/pull/767#issuecomment-3031095234  

> I wonder what is the blast radius of this fix w.r.t. older compilers  
  
I can test older clang++-17 on MacOS  
```text  
anonymous@macbook tmp % cat main.cpp  
#include <boost/gil.hpp>  
  
int main() {}  
anonymous@macbook tmp % /usr/bin/clang++ --version  
Apple clang version 17.0.0 (clang-1700.0.13.5)  
Target: arm64-apple-darwin24.5.0  
Thread model: posix  
InstalledDir: /Library/Developer/CommandLineTools/usr/bin  
anonymous@macbook tmp % /usr/bin/clang++ -I/opt/homebrew/include main.cpp -o main  
In file included from main.cpp:1:  
In file included from /opt/homebrew/include/boost/gil.hpp:12:  
/opt/homebrew/include/boost/gil/algorithm.hpp:1241:54: error: a template argument list is expected after a name prefixed by the template keyword [-Wmissing-template-arg-list-after-template-kw]  
 1241 |         return inner_product_k_t<Size - 1>::template apply(  
      |                                                      ^  
/opt/homebrew/include/boost/gil/algorithm.hpp:1288:54: error: a template argument list is expected after a name prefixed by the template keyword [-Wmissing-template-arg-list-after-template-kw]  
 1288 |     return detail::inner_product_k_t<Size>::template apply(  
      |                                                      ^  
2 errors generated.  
```  
(Sorry but I cannot test compilers older than that only by myself, because MacOS has clang-version fixed and my another computer is ArchLinux with everything including gcc roll-upgrading)  
  
But logically, I can tell that all compiler **should** accept the one without `template`. Let's see what the `template` keyword do here.  
Suppose this situation:  
```cpp  
template <class T>  
struct A  
{  
    template <int Num> f() { return Num; };  
};  
  
template <>  
struct A<float>  
{  
    static constexpr float f = 42;  
};  
  
A<int>   a; a.f<1>()// case 1  
A<float> a; a.f<1>()// case 2  
```  
If we call this function like `a.f<1>()`, once compiler read `a.f<1...` it will possibly see 2 possibilities:`a./*call-func*/f<1>()`, or "`a.f` is less than `1`" ? When `T == int`, only the first one is ok because `A<int>.f` is a function; when `T == float`, only the second one is explainable because: `A<float>.f` is a value.  
  
So that, we use a `template` keyword to avoid this misleading situation. [Cppreference](https://cppreference.com/w/cpp/language/dependent_name.html) also says that a `template` keyword is needed here and only here.  
  
(You can also find another example in neighbor `boost.asio`)  
```cpp  
template <class Executor = boost::asio::any_io_executor>  
struct boost::asio::ip::tcp::basic_socket  
{  
    template <class OtherExecutor>  
    using rebind_executor = basic_socket<OtherExecutor>;  
};  
using my_socket = basic_socket<system_executor>::template rebind_executor<typename io_context::executor>;  
```  
  
Actually, `gcc` has a weaker grammar checking: both `a.template f<1>()` and `a.f<1>()` are accepted: if you input `a.f<1>()`, compiler will automatically choose `[int] a.f<1>()` or `[float] (a.f < 1) > ...` in syntax-checking stage, meanwhile the oppose case `b.template implicit_deduction_template_func(...)` is accepted too. But `clang` (in most times) is far more strict to standard and has a far more strict AST: in both situations above, `clang` only accepts `[int] a.template f<1>()` and `b.implicit_deduction_template_func(...)`.  
  
*(Just a few days before I reported a GCC internal-compiler-error which happens on grammar-parsing. Finally gcc-contributors found both gcc and clang have the bug. But once I read the [gcc-source](https://github.com/gcc-mirror/gcc/blob/master/gcc/cp/parser.cc) [clang-source](https://github.com/llvm/llvm-project/blob/main/clang/include/clang/AST/ASTContext.h) of both compiler, I found that gcc has an AST far more complexed, complicated, and hard to maintain than clang. See [gcc-bug](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=120845) and [clang-bug](https://github.com/llvm/llvm-project/issues/146469))*  
   
Back to `boost.gil`. The problem is that: the source code in `boost.gil` is not misleading at all:  
```cpp  
// boost/libs/gil/include/boost/gil/algorithm.hpp:1240  
init = binary_op1(init, binary_op2(*first1, *first2));  
        return inner_product_k_t<Size - 1>::template apply(  
            first1 + 1, first2 + 1, init, binary_op1, binary_op2);  
```  
You see: this `apply` here is **never** misleading: no template `<` follows it. The compiler always knows here is a "function-call", not a "less-than comparation". So the `template` keyword is always redundant in all cases.  
  
**Thank you very much!**

---

## Comment 3

> Username: mloskot  
> Created_at: 2025-07-03 12:52:48 UTC  
> Url: https://github.com/boostorg/gil/pull/767#issuecomment-3032159437  

Awesome! Thank you very much for the explanation.  
It is just that nowadays I'm a bit swamped with non C++ activities and don't have a fully C++-enabled workstation handy as I used to have. And, some of our CI builds based on GitHub Actions are broken.  
  
I'm happy to merge it.

---

## Comment 4

> Username: shyeyian  
> Created_at: 2025-07-11 07:17:26 UTC  
> Url: https://github.com/boostorg/gil/pull/767#issuecomment-3060946692  

@mloskot Hello, could you please merge branch `develop` into branch `master`?   
  
(I love this library. But for me, who always clones `boost` superproject, checking out to `develop` only on one submodule brings many troubles, especially when syncing with servers in school.)  
  
Thank you! :)

---

## Comment 5

> Username: mloskot  
> Created_at: 2025-07-11 07:27:34 UTC  
> Url: https://github.com/boostorg/gil/pull/767#issuecomment-3061013327  

@anonymouspc I'd love to get recent changes merged to `master` but it needs some prep.  
I'm worried about the failing CI jobs which I constantly have no time to look at.

---
