# #385 - X3: Unwrap forward_ast in visitor (like variant does for recursive_wrapper) [Open]

> Username: dvirtz  
> Created at: 2018-05-02 20:29:36 UTC  
> Updated at: 2019-03-03 23:17:41 UTC  
> Url: https://github.com/boostorg/spirit/issues/385  

unlike boost::recursive_wrapper, x3::forward_ast binds better to auto than to the underlying type.  
  
```c++  
#include <boost/variant.hpp>  
#include <iostream>  
#include <type_traits>  
#include <utility>  
#include <boost/spirit/home/x3/support/ast/variant.hpp>  
  
template <typename...> struct overload_set {  
  void operator()() {}  
};  
  
template <typename Func, typename... Funcs>  
struct overload_set<Func, Funcs...> : Func, overload_set<Funcs...> {  
  using Func::operator();  
  using overload_set<Funcs...>::operator();  
  
  overload_set(const Func &f, const Funcs &... fs)  
      : Func(f), overload_set<Funcs...>(fs...) {}  
};  
  
template <typename... Funcs> auto overload(Funcs &&... fs) {  
  using os = overload_set<typename std::remove_reference<Funcs>::type...>;  
  return os(std::forward<Funcs>(fs)...);  
}  
  
struct Rec;  
using Var = boost::variant<int, boost::recursive_wrapper<Rec>>;  
struct Rec {  
  Var var;  
};  
  
namespace x3 = boost::spirit::x3;  
struct Rec2;  
using Var2 = x3::variant<int, x3::forward_ast<Rec2>>;  
struct Rec2 {  
  Var2 var;  
};  
  
int main() {  
  auto f = overload([](Rec) { std::cout << "Rec\n"; },  
                    [](const auto &a) { std::cout << "other\n"; });  
  boost::apply_visitor(f, Var{Rec{}});  
  
  auto f2 = overload([](Rec2) { std::cout << "Rec\n"; },  
                     [](const auto &a) { std::cout << "other\n"; });  
  boost::apply_visitor(f2, Var2{Rec2{}});  
}  
```  
  
output is   
```  
Rec  
other  
```  
  
Boost 1.64  
Tested on MSVC 15.7 and Clang 5

---

## Comment 1

> Username: Kojoley  
> Created at: 2019-03-02 22:35:48 UTC  
> Url: https://github.com/boostorg/spirit/issues/385#issuecomment-468967104  

It is because `x3::variant` do not unwrap `x3::forward_ast` so the type your visitor receiving is `x3::forward_ast<Rec2>`.  
  
@djowel is this bug or not?

---

## Comment 2

> Username: djowel  
> Created at: 2019-03-03 00:08:00 UTC  
> Updated at: 2019-03-03 00:16:20 UTC  
> Url: https://github.com/boostorg/spirit/issues/385#issuecomment-468972844  

I'm not sure. And explicit is always better than implicit. I'm open to opinions.

---

## Comment 3

> Username: Kojoley  
> Created at: 2019-03-03 00:13:52 UTC  
> Url: https://github.com/boostorg/spirit/issues/385#issuecomment-468973144  

> Implicit is always better than explicit.  
  
Hmm, when I was making x3::variant constructor implicit you said it must not :-)

---

## Comment 4

> Username: djowel  
> Created at: 2019-03-03 00:17:03 UTC  
> Url: https://github.com/boostorg/spirit/issues/385#issuecomment-468973328  

> Hmm, when I was making x3::variant constructor implicit you said it must not :-)  
  
Gah, not enough coffee :-) Comment edited.

---

## Comment 5

> Username: Kojoley  
> Created at: 2019-03-03 00:22:03 UTC  
> Url: https://github.com/boostorg/spirit/issues/385#issuecomment-468973578  

Because the change will break user code and most likely 1.71 `boost::variant` will not have performance issues with `recursive_wrapper` I am inclined to close this as wontfix.

---

## Comment 6

> Username: djowel  
> Created at: 2019-03-03 00:28:07 UTC  
> Url: https://github.com/boostorg/spirit/issues/385#issuecomment-468973910  

> Because the change will break user code and most likely 1.71 `boost::variant` will not have performance issues with `recursive_wrapper` I am inclined to close this as wontfix.  
  
Good point.

---

## Comment 7

> Username: Kojoley  
> Created at: 2019-03-03 01:31:19 UTC  
> Url: https://github.com/boostorg/spirit/issues/385#issuecomment-468977344  

> explicit is always better than implicit.  
  
I cannot agree with this. If it was so there would be no ADL, operator overloading, template argument deduction, `auto` keyword, return type deduction and Spirit itself.

---

## Comment 8

> Username: djowel  
> Created at: 2019-03-03 01:39:07 UTC  
> Updated at: 2019-03-03 01:45:05 UTC  
> Url: https://github.com/boostorg/spirit/issues/385#issuecomment-468977701  

> I cannot agree with this. If it was so there would be no ADL, operator overloading, template argument deduction, `auto` keyword, return type deduction and Spirit itself.  
  
You know it's from the The Zen of Python, right? It's not a hard rule of course. I typically prepend that by "in general, " explicit is always better than implicit. And also "When in doubt, ...".   
  
It does not say that you should never use implicit.

---

## Comment 9

> Username: dvirtz  
> Created at: 2019-03-03 20:31:41 UTC  
> Url: https://github.com/boostorg/spirit/issues/385#issuecomment-469061074  

Sorry that I'm late for the discussion but if I understand correctly you're suggesting the handler will take an `x3::forward_ast<Reg>`?   
Looking at example code, I don't see handlers do this, e.g. in https://github.com/boostorg/spirit/blob/b4c5ef702bf6c28e964a84c9e9abe1a6549bce69/example/x3/calc/calc4.cpp, and I believe adding an `auto` overload will break them too.

---

## Comment 10

> Username: Kojoley  
> Created at: 2019-03-03 21:11:15 UTC  
> Url: https://github.com/boostorg/spirit/issues/385#issuecomment-469064659  

It is possible because `x3::forward_ast<T>` has implicit conversions to `T&`/`T const&` https://github.com/boostorg/spirit/blob/f6297dc92f0dc623910cd978b3a9f56437ec242d/include/boost/spirit/home/x3/support/ast/variant.hpp#L87-L88  
  
I looked for them yesterday but missed. This makes possible to unwrap the `forward_ast` in visitor, like `boost::variant` does for `recursive_wrapper`, but I am not disposed to make this myself. PR is welcomed.

---

## Comment 11

> Username: djowel  
> Created at: 2019-03-03 23:17:41 UTC  
> Url: https://github.com/boostorg/spirit/issues/385#issuecomment-469075878  

> Looking at example code, I don't see handlers do this, e.g. in https://github.com/boostorg/spirit/blob/b4c5ef702bf6c28e964a84c9e9abe1a6549bce69/example/x3/calc/calc4.cpp, and I believe adding an `auto` overload will break them too.  
  
Ah right, I forgot about the conversions. At any rate, to support this, we'll need to do the visitation ourselves, instead of forwarding. Not a simple task.   
  
Yes, auto will break those too.
