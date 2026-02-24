# #100 - Regression after 1.62: `variant<recursive_wrapper<X>, ...>` is not constructible from `X`  when `X` is a variant [Open]

> Username: Kojoley  
> Created at: 2022-08-08 22:28:37 UTC  
> Updated at: 2022-09-02 05:50:11 UTC  
> Url: https://github.com/boostorg/variant/issues/100  

Regressed in https://github.com/boostorg/variant/commit/927280570a553b43846e74ddd6b3802d5a1a5f1c  
```cpp  
#include <boost/variant.hpp>  
  
struct Nil {};  
  
typedef boost::variant<  
                Nil  
              , double  
            >  
        Atom;  
  
typedef boost::variant<  
                Nil  
#if 1  
                  , boost::recursive_wrapper<Atom>  
#else  
                  , Atom  
#endif  
                >  
        Base;  
  
int main()  
{  
    Atom atom;  
    Base base = atom;  
}  
```  
  
Removing these specializations fixes the compilation https://github.com/boostorg/variant/blob/d2fdf2384b146b0c764077f5539f5f6ed7c40f20/include/boost/variant/recursive_wrapper_fwd.hpp#L60-L67

---

## Comment 1

> Username: apolukhin  
> Created at: 2022-09-01 11:28:13 UTC  
> Url: https://github.com/boostorg/variant/issues/100#issuecomment-1234147961  

Compile time error is actually a very good outcome!  
  
`recursive_wrapper` is inefficient, it should not be used in this case.  
  
In cases when `recursive_wrapper` is used for actual recursion the `is_constructible` trait specializations are required. Without the specializations the trait is called on incomplete type and either a wrong trait answer is memorized by the compiler or the static assert from within the type trait is raised.  
  
Anyway, I find explicitness in those variant1<>variant2 conversions a very good feature. In your example, should the variant hold `Nil` as the 0th-`Nil` or as a part of `Atom`? I'm quite sure that different developers would answer differently, which makes that conversion error prone and poorly maintainable.  
  
Fell free to reopen this ticket, if there are some real world examples where the old behavior really required.

---

## Comment 2

> Username: Kojoley  
> Created at: 2022-09-01 13:39:19 UTC  
> Url: https://github.com/boostorg/variant/issues/100#issuecomment-1234296456  

> recursive_wrapper is inefficient, it should not be used in this case.  
  
I think you are missing the other side here. Let's say `Atom` is a huge type, storing it directly in `Base` blows it size substantially, and you have a large container of `Base`s where `Atom` is rarely presented -- I see in such a case utilizing `recursive_wrapper` to minimize `Base` layout is justifiable.  
  
> I'm quite sure that different developers would answer differently, which makes that conversion error prone and poorly maintainable.  
  
[The documentation says](https://www.boost.org/doc/libs/develop/doc/html/variant/tutorial.html#variant.tutorial.advanced:~:text=Because%20variant%20provides%20special%20support%20for%20recursive_wrapper%2C%20clients%20may%20treat%20the%20resultant%20variant%20as%20though%20the%20wrapper%20were%20not%20present.):  
> Because variant provides special support for recursive_wrapper, clients may treat the resultant variant as though the wrapper were not present.  
  
The thing is that the previously valid code is broken without any notice and it had not been documented to the present day.  
  
> Fell free to reopen this ticket, if there are some real world examples where the old behavior really required.  
  
I don't care that much of this issue, but it blocks migration from old boost versions when there is really no bugs in the user code, and no way to at least temporary turn this 'diagnostic' off. See https://stackoverflow.com/questions/73263043/updating-boostvariant-from-boost-1-60-to-boost-1-79

---

## Comment 3

> Username: apolukhin  
> Created at: 2022-09-02 05:50:10 UTC  
> Url: https://github.com/boostorg/variant/issues/100#issuecomment-1235093190  

Yor're right, at least the docs should be changed
