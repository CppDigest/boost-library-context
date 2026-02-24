# #72 - compare_pointees.hpp should permit a predicate [Open]

> Username: BenFrantzDale  
> Created at: 2021-02-05 13:42:40 UTC  
> Updated at: 2022-10-07 12:55:28 UTC  
> Url: https://github.com/boostorg/utility/issues/72  

Suppose I have  
```  
std::optional<std::pair<int, float>> a, b;  
```  
and I want to test equality (or less-than-ness) of the `float` part, still treating them as optional. That is, thinking of them as optional `float`s. If `equal_pointees` had a predicate argument, I could do  
```  
equal_pointees(a, b, [](auto& x, auto& y) { return std::get<float>(x) == std::get<float>(y); });  
```  
The particular case I'd use this is where I've got a big struct representing the state of a view. For the current frame, I have a pointer to the previous one and a pointer to the current one and I want to decide if I have to a certain part of the work of redrawing. That is, either they have different nullness that's different, or if they are both non-null then compare a projection of them that pertains to the thing I care about.

---

## Comment 1

> Username: BenFrantzDale  
> Created at: 2021-02-16 19:25:20 UTC  
> Url: https://github.com/boostorg/utility/issues/72#issuecomment-780064198  

For that matter, I should be able compare pointees of different type, provided `operator==` applies to both dereferenced. That is, I think it should be  
```  
template<class OptionalPointeeX, class OptionalPointeeY, class EqualTo = std::equal_to<>>  
inline  
[[nodiscard]] bool equal_pointees ( OptionalPointeeX const& x, OptionalPointeeY const& y, EqualTo equal_to = {})  
{  
  return (!x) != (!y) ? false : ( !x ? true : equal_to(*x, *y) );  
}  
```

---

## Comment 2

> Username: BenFrantzDale  
> Created at: 2022-10-07 12:26:15 UTC  
> Updated at: 2022-10-07 12:55:28 UTC  
> Url: https://github.com/boostorg/utility/issues/72#issuecomment-1271524893  

I thought more about this. I think(?) this is the basis function we'd really want:  
```  
template <typename OptA, typename OptB,  
          typename Cmp = std::compare_three_way,  
          typename Proj = std::identity>  
[[nodiscard]]   
auto pointees_compare(const OptA& a, const OptB& b,   
                      Cmp cmp = {},  
                      Proj proj = {})  
     -> decltype(cmp(std::invoke(proj, *a), std::invoke(proj, *b)))   
{  
    if (a and b) {  
        return cmp(std::invoke(proj, *a), std::invoke(proj, *b));  
    }  
    return cmp(static_cast<bool>(a), static_cast<bool>(b));  
}  
```  
Compared with the existing `equal_pointees`, it...  
1. Allows you to compare a `std::optional<int>` with a `const float*` (the pointer-like types can be different). This also means if you have a `std::optional<BigClass> a` and a `BigClass b`, you can call `pointees_compare(a, &b)`.  
2. Allows you to project the dereferenced objects, so you can conceptually compare `a->x` with `b->x` by using `[](auto& a) { return a.x; }` as the projection function.  
3. By using `std::invoke`, it lets you pass `&Class::memfun` as the projection.  
4. It supports equality, less-than, three-way, and others.  
5. The only other thing I could see adding here is having the nullness comparison use a special type, so the caller could overload their `cmp` to do something special, like compare two `int*`s where `nullptr` compares "more" than not-null, but the ints compare in numerical order. (That is, `struct truthiness_t { bool truthy; explicit truthiness_t(auto& x) : truthy(static_cast<bool>(x)) {} };` and then `return cmp(truthiness_t(a), truthiness_t(b);` But that seems like overkill. https://godbolt.org/z/ejKW7dd55  
6. We could also allow for special null types, like `std::nullptr_t` and `std::nullopt_t` that can't be dereferenced, but that seems like a lot of extra work, and inelegant.  
  
It's a basis in that the existing `equal_pointees` can be implemented in terms of it as  
```  
template<class OptionalPointee>  
inline  
bool equal_pointees ( OptionalPointee const& x, OptionalPointee const& y )  
{  
  return pointees_compare(x, y, std::equal_to{});  
}  
```  
and `less_pointees`:  
```  
template<class OptionalPointee>  
inline  
bool less_pointees ( OptionalPointee const& x, OptionalPointee const& y )  
{  
  return pointees_compare(x, y, std::less{});  
}  
```  
I keep coming back to this because I have an old function that has a wall of `if (needToUpdateX(oldStatePtr, newStatePtr)) {...}` where `needToUpdateX` is `true` if the pointers have different nullness or if both are valid but `oldStatePtr->X() != newStatePtr->X()`, so all of these `needToUpdateX` functions could be replaced by `pointes_compare(oldStatePtr, newStatePtr, std::not_equal_to{}, &MyClass::X)`.  
  
https://godbolt.org/z/6EEb4GKc9
