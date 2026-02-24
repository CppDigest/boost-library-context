# #467 - hana::fix doesn't (consistently) work on lambdas with deduced return type [Open]

> Username: ecatmur  
> Created at: 2020-06-04 23:18:28 UTC  
> Updated at: 2020-06-11 13:18:16 UTC  
> Url: https://github.com/boostorg/hana/issues/467  

[Consider ](https://godbolt.org/z/vzZVqA) a recursive lambda with deduced return type and multiple return statements:  
  
    auto twice= fix([](auto f, int i) {  
        if (i <= 0)  
            return 0;  
        return 2 + f(i - 1);  
    });  
    auto i = twice(1);  
  
This doesn't work; gcc reports  
  
    error: use of 'constexpr decltype(auto) boost::hana::fix_t<F>::operator()(X&& ...) & [with X = {int}; F = <lambda(auto:1, int)>]' before deduction of 'auto'  
       38 |         return 2 + f(i - 1);  
          |                    ~^~~~~~~  
  
But if `twice` is const or rvalue, it does work. This is [confusing and inconsistent](https://stackoverflow.com/questions/62161963/return-type-deduction-of-recursive-function).  
  
By [dcl.spec.auto]/10, the return type of the lambda itself (rather, of a particular instantiation of its call operator template) is determined by the first return statement and is available thereafter within that call operator; all three compilers agree that this holds for recursive calls to that same instantiation of the call operator template. The problem is that the return type of `fix_t<F>::operator()<int> &` is also undergoing type deduction at this point, and because it only has a single return statement [dcl.spec.auto]/10 can't help.  
  
The first step in a solution is to look at why the const (and rvalue) cases work. This is because when `fix_t<F>::operator()<int> const&` calls the lambda, its call to `fix(f)` does not apply const qualification to the result, so the lambda receives a `fix_t<F>` as its first argument and its recursive call invokes `fix_t<F>::operator()<int> &`, which is not yet undergoing return type deduction. Then, when the compiler does attempt return type deduction on `fix_t<F>::operator()<int> &` it notes that its call is to `F::operator()<fix_t<F>, int>` which has already been deduced as returning `int` by [dcl.spec.auto]/10.   
  
Adding a `bool` template parameter would work for the simple case, but more complicated cases are possible where the lambda has other polymorphic arguments. The general solution, due to aschepler on SO, is to record all the argument lists seen so far and, for each new argument list (including the initial call), generate a new fixpoint wrapper (whose `operator()`s are not yet undergoing return type deduction and are therefore safe to call from the lambda). (Note that the initial call must supply a fixpoint wrapper distinct from itself to the lambda.) Conversely, if the argument list has already been seen in a particular call chain, we know the lambda has already been called with the current fixpoint wrapper and argument types, so if it has a stable return type at all this will already have been deduced and so it is safe to reenter with the same current fixpoint wrapper. The elegant if obvious trick is to use the list of argument lists themselves as the uniquifying parameter:  
  
```  
template <class F>  
struct fix_t {  
    template <class Ff, class... TLs>  
    struct ref {  
        Ff ff;  
        template <class... Args>  
        constexpr decltype(auto) operator()(Args&&... args) const {  
            using G = std::conditional_t<(std::is_same_v<F(Args...), TLs> || ...), ref, ref<Ff, TLs..., F(Args...)>>;  
            return static_cast<Ff>(ff)(G{static_cast<Ff>(ff)}, std::forward<Args>(args)...);  
        }  
    };  
    F f;  
    template <class... Args>  
    constexpr decltype(auto) operator()(Args&&... args) const& {  
        return ref<F const&>{f}(std::forward<Args>(args)...);  
    }  
    template <class... Args>  
    constexpr decltype(auto) operator()(Args&&... args) & {  
        return ref<F&>{f}(std::forward<Args>(args)...);  
    }  
    template <class... Args>  
    constexpr decltype(auto) operator()(Args&&... args) && {  
        return ref<F&&>{std::move(f)}(std::forward<Args>(args)...);  
    }  
};  
```  
  
I'm happy to submit a PR but would appreciate direction on naming and on whether the above using function types for argument lists and `<type_traits>` to generate new fixpoint wrappers is an acceptable implementation.

---

## Comment 1

> Username: ricejasonf  
> Created at: 2020-06-11 00:35:03 UTC  
> Url: https://github.com/boostorg/hana/issues/467#issuecomment-642337216  

Greetings!  
  
My feedback is not authoritative, but including `<type_traits>` is perfectly fine. Nothing internal depends on it, and it is included in other files. Naming of implementation detail stuff seems like a non-issue.  
  
As an alternative, `boost::hof::fix` compiles with your example. I don't claim to fully understand how it works, but it appears to have a base class that depends on a `fix_result` template that gets the result of the input function.  
https://godbolt.org/z/Jf4W-q  
  
I do remember that the author wanted to downplay the use of `functional` in favor of promoting the use of Boost.Hof, but I don't see why we shouldn't ...... **fix** this. :rofl:   
  
... considering you already have a solution.

---

## Comment 2

> Username: ecatmur  
> Created at: 2020-06-11 13:18:16 UTC  
> Url: https://github.com/boostorg/hana/issues/467#issuecomment-642641862  

Boost.Hof is definitely better, but it has limited recursion depth in constexpr, and it doesn't work with some slightly more complicated cases: https://godbolt.org/z/XUmfwD  
  
Having looked at it some more, the dance with typelists is unnecessary and actually fails in some cases. This is simpler:  
  
```  
template <class F>  
struct fix_t {  
    template <class Ff>  
    struct ref {  
        Ff ff;  
        template <class... Args>  
        constexpr decltype(auto) operator()(Args&&... args) const {  
            return static_cast<Ff>(ff)(ref{static_cast<Ff>(ff)}, std::forward<Args>(args)...);  
        }  
    };  
    F f;  
    template <class... Args>  
    constexpr decltype(auto) operator()(Args&&... args) const& {  
        return f(ref<F const&>{f}, std::forward<Args>(args)...);  
    }  
    template <class... Args>  
    constexpr decltype(auto) operator()(Args&&... args) & {  
        return f(ref<F&>{f}, std::forward<Args>(args)...);  
    }  
    template <class... Args>  
    constexpr decltype(auto) operator()(Args&&... args) && {  
        return std::move(f)(ref<F&&>{std::move(f)}, std::forward<Args>(args)...);  
    }  
};  
```
