# #91 - A combination of alternative and sequence parsers make the attribute incompatible with the expected result type [Closed]

> Username: akrzemi1  
> Created at: 2024-02-03 16:46:57 UTC  
> Updated at: 2024-02-07 23:32:03 UTC  
> Closed at: 2024-02-04 22:36:57 UTC  
> Url: https://github.com/boostorg/parser/issues/91  

The following example fails to compile. The `parse()` that initializes `b1` compiles fine, but the one that initializes `b2` does not.  
https://godbolt.org/z/M4aTb5c9a  
  
```c++  
struct KeyVal {std::string key, val; };  
  
namespace parser  
{  
    const auto string = bp::lexeme[+bp::char_];  
    const auto keyVal = '[' >> string >> string >> ']';  
    const auto alternative = bp::int_ | keyVal;  
}  
  
int main()  
{  
    std::variant<int, KeyVal> result;  
    KeyVal kv;  
  
    auto b1 = bp::parse("[a b]", parser::keyVal, bp::ws, kv);  
    auto b2 = bp::parse("[a b]", parser::alternative, bp::ws, result);  
}  
```  
  
If `keyVal` can assign to `KeyVal`, I would expect `int_ | keyVal` to assign to `variant<int, KeyVal>`.

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-02-04 21:42:59 UTC  
> Updated at: 2024-02-04 21:46:11 UTC  
> Url: https://github.com/boostorg/parser/issues/91#issuecomment-1925931377  

The problem here is not with the combination of sequence and alternative parsers.  The problem is trying to assign a `tuple<std::string, std::string>` to a `KeyVal` *inside* a variant.  
  
This is not something that can be overcome with a bit of metaprogramming.  Consider this.  
  
```c++  
struct foo_1 { int i; double d; };  
struct foo_2 { double d; int i; };  
  
using var_t = std::variant<foo_1, foo_2>;  
using tup_t = boost::parser::tuple<short, short>;  
  
var_t v = /* ... */;  
tup_t t = /* ... */;  
some_assignment(v, t);  
```  
  
What should `some_assignment` do?  Should it silently choose either `foo_1` or `foo_2` as a better match?  Should this happen silently?  This is an obvious example.  It's possible to make even subtler ones, like having one alternative work, but be a worse match, but actually be the one the user expected.  
  
IOW, I'll underline the fact that we only assign to variants, and explicitly call out the lack of tuple -> aggregate (or vice versa) transformations.

---

## Comment 2

> Username: akrzemi1  
> Created at: 2024-02-07 21:30:44 UTC  
> Url: https://github.com/boostorg/parser/issues/91#issuecomment-1932970588  

Assigning arbitrary types to arbitrary variants -- as a general problem -- is unsolvable. But for the purpose of Boost.Prser, it would be sufficient to solve a specific case.  
  
How about the following. When assigning type `X` to `variant<Ts...>`:  
  
 1. When there is no direct match for `X` in `Ts`, and  
 2. `X` is an instance of `tuple` (be it `hana::tuple` or `std::tuple`)  
  
Then if there is a `T` in `Ts` that is an aggregate layout-compatible with `X`, choose this `T` as the assignment target.  
  
If there are two such types in `Ts`, I fail to see how this would negatively impact the user if you picked either.

---

## Comment 3

> Username: akrzemi1  
> Created at: 2024-02-07 21:53:25 UTC  
> Url: https://github.com/boostorg/parser/issues/91#issuecomment-1933000281  

Sorry, "layout-compatible" is not the answer, as it requires standard-layout types recursively.   
  
Instead, let's define and use term *member-compatible* defined as follows.  
  
An aggregate `A` is *member-compatible* with `tuple` `T` when the sequence of members in `A` is same as the sequence of elements in `T`.

---

## Comment 4

> Username: tzlaine  
> Created at: 2024-02-07 23:27:56 UTC  
> Url: https://github.com/boostorg/parser/issues/91#issuecomment-1933109093  

> Then if there is a `T` in `Ts` that is an aggregate layout-compatible with `X`, choose this `T` as the assignment target.  
>   
> If there are two such types in `Ts`, I fail to see how this would negatively impact the user if you picked either.  
  
If there is an ambiguity and I pick one, that's a serious problem IMO.

---

## Comment 5

> Username: tzlaine  
> Created at: 2024-02-07 23:32:02 UTC  
> Url: https://github.com/boostorg/parser/issues/91#issuecomment-1933113453  

> Sorry, "layout-compatible" is not the answer, as it requires standard-layout types recursively.  
>   
> Instead, let's define and use term _member-compatible_ defined as follows.  
>   
> An aggregate `A` is _member-compatible_ with `tuple` `T` when the sequence of members in `A` is same as the sequence of elements in `T`.  
  
It's actually more loose than that.  If a tuple and an aggregate have the same number of elements, and one could assign the elements of one to the other, then they are compatible.  Note that this might work in only one direction.  
  
There's already a metafunction that does this recursive compatibility check.  It currently just uses assignability for variants.  If I were to change it to look at all the vairant types, figure out which ones might work -- including via tuple->aggregate or aggregate->tuple or tuple->cosntructor compatibility -- and then in a SFINAE-friendly manner only succeed if exactly one of them works, I would explode compile times to make this one corner smoother.  
  
I don't think it's a very good trade.
