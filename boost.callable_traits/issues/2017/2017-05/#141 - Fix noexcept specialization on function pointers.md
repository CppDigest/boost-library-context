# #141 - Fix noexcept specialization on function pointers [Closed]

> Username: mmha  
> Created at: 2017-05-28 22:07:49 UTC  
> Updated at: 2017-05-29 18:26:39 UTC  
> Closed at: 2017-05-29 18:26:39 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/141  

I just (indirectly) tried to use your library. Unfortunately, the specialization on `noexcept` of function pointers (#89) does not work on clang:  
  
```  
template<typename T>  
struct S;  
  
template<bool IsNoexcept>  
struct S<void(*)() noexcept(IsNoexcept)> {  
	S() {}  
};  
  
void f() {}  
  
int main() {  
	S<decltype(&f)> {};  
}  
```  
Clang output:  
```  
struct S<void(*)() noexcept(IsNoexcept)> {  
       ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
example.cpp:4:15: note: non-deducible template parameter 'IsNoexcept'  
template<bool IsNoexcept>  
              ^  
example.cpp:12:2: error: implicit instantiation of undefined template 'S<void (*)()>'  
        S<decltype(&f)> {};  
        ^  
example.cpp:2:8: note: template is declared here  
struct S;  
       ^  
```  
In fact, just including callable_traits causes clang to emit those errors in C++17 mode. The same code [crashes gcc](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=80908), removing the constructor is a workaround and results in a successful compilation.  
  
I cannot back it up with a reference to the standard, but the clang implementation suggests that this is not legal C++17. I don't trust the gcc implementation in that regard because of the bug I found.

---

## Comment 1

> Username: badair  
> Created at: 2017-05-29 16:53:06 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/141#issuecomment-304700192  

Thank you for the report; this is quite unfortunate. I asked about this [here](https://stackoverflow.com/questions/44247151). libstdc++ uses this technique for its definition of `is_function`, which makes me think this is a QOI issue that I'll need to work around.  
  
Relates to #85

---

## Comment 2

> Username: badair  
> Created at: 2017-05-29 17:22:48 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/141#issuecomment-304704059  

I used this technique to cut down on the amount of preprocessed text in half when compiling with c++1z. I'll start working to revert this optimization, but it will increase the preprocessed LOC from 20k to 40k :(

---

## Comment 3

> Username: badair  
> Created at: 2017-05-29 18:26:39 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/141#issuecomment-304711421  

closed via e310b2b2280a42aabcbcc8f732fe3e3103548998. Bumped version to 2.0.0 which includes all post-review changes so far.
