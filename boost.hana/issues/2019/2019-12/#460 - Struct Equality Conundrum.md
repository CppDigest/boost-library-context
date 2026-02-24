# #460 - Struct Equality Conundrum [Closed]

> Username: ricejasonf  
> Created at: 2019-12-13 00:47:45 UTC  
> Updated at: 2020-09-03 15:49:30 UTC  
> Closed at: 2020-09-03 15:49:30 UTC  
> Url: https://github.com/boostorg/hana/issues/460  

> Original question on StackOverflow https://stackoverflow.com/questions/59250210/how-do-i-implement-equality-compare-operators-for-boost-hana-structs  
  
Currently, if the user implements equality operators for their own `Struct`, **ambiguous template instantiation** will ensue because it satisfies both `Struct` and `detail::EqualityComparable`.  
  
Should we allow custom operators on Struct for equality? The motivation is that some standard library types require them for their contained type such as `std::optional`.  
  
```cpp  
#include <boost/hana.hpp>  
  
namespace hana = boost::hana;  
  
struct SomeStruct {  
  BOOST_HANA_DEFINE_STRUCT(SomeStruct, (int, x), (int, y), (char, c));  
  
  constexpr bool operator == (SomeStruct const& other) {  
    return boost::hana::equal(boost::hana::members(*this),  
                              boost::hana::members(other));  
  }  
  
  constexpr bool operator != (SomeStruct const& other) {  
    return boost::hana::not_equal(boost::hana::members(*this),  
                                  boost::hana::members(other));  
  }  
};  
  
// horrible workaround  
namespace boost::hana::detail {  
  template <>  
  struct EqualityComparable<SomeStruct> : std::false_type { };  
}  
  
int main() {  
    // OK  
    static_assert(SomeStruct{5, 5, 5} == SomeStruct{5, 5, 5});  
    // FAIL (ambiguous template instantiation with detail::EqualityComparable  
    //       without horrible workaround)  
    static_assert(hana::equal(SomeStruct{5, 5, 5}, SomeStruct{5, 5, 5}));  
}  
```  
https://godbolt.org/z/Eu64Ng

---

## Comment 1

> Username: mjhurd  
> Created at: 2020-02-20 16:56:51 UTC  
> Url: https://github.com/boostorg/hana/issues/460#issuecomment-589182703  

Thanks for the note. Is there a simple way to do this inline w.r.t. to the original struct def? (Asking for a macro friend ;-)

---

## Comment 2

> Username: ricejasonf  
> Created at: 2020-02-20 17:52:43 UTC  
> Url: https://github.com/boostorg/hana/issues/460#issuecomment-589223739  

You could possibly use a base class and use SFINAE with `std::is_base_of` in that `detail::EqualityComparable` template.  
  
```  
    struct NotEqualityComparableBase { };  
  
    template <typename T>  
    struct EqualityComparable<T, T, std::enable_if_t<  
        std::is_base_of<NotEqualityComparableBase, T>::value  
    >> : std::false_type {  };  
```  
  
There is definitely a bug here. I looked into it a bit. I'm not sure if there is a chicken and egg problem with `detail::EqualityComparable` and `equal_impl`, but I think `detail::EqualityComparable` itself should be specialized to `std::false_type` if `equal_impl` is not default.

---

## Comment 3

> Username: mjhurd  
> Created at: 2020-02-20 18:07:01 UTC  
> Url: https://github.com/boostorg/hana/issues/460#issuecomment-589230108  

Thanks again. I have a solution for now for my ugly macros. I do think you're right that it would be better if the library handled with `std::false_type` as you suggest.

---

## Comment 4

> Username: ldionne  
> Created at: 2020-09-03 15:49:30 UTC  
> Url: https://github.com/boostorg/hana/issues/460#issuecomment-686583897  

Fixed in 4f5157bc425eac8e115d597142932fef2e535282, thanks Jason.
