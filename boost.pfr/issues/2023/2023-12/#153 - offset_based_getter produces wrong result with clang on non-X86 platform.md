# #153 - offset_based_getter produces wrong result with clang on non-X86 platform [Open]

> Username: varlax77  
> Created at: 2023-12-04 09:12:15 UTC  
> Updated at: 2023-12-06 00:16:10 UTC  
> Url: https://github.com/boostorg/pfr/issues/153  

The attached mini reproducer fails if compiled with Clang-16 on non-X86 platform:  
  
```  
#include <boost/pfr/detail/offset_based_getter.hpp>  
#include <boost/pfr/detail/sequence_tuple.hpp>  
  
#include <boost/core/lightweight_test.hpp>  
  
struct user_type {  
    char c;  
    double d;  
};  
  
int main() {  
   using pfr_tuple = boost::pfr::detail::sequence_tuple::tuple<char, double>;  
   using getter = boost::pfr::detail::offset_based_getter<user_type, pfr_tuple>;  
   using boost::pfr::detail::size_t_;  
   using boost::pfr::detail::sequence_tuple::get;  
  
   user_type value{};  
   auto begin = reinterpret_cast<char*>(&value);  
   auto native_offset = reinterpret_cast<char*>(&value.d) - begin;  
  
   auto getter_offset = reinterpret_cast<char*>(&getter{}.get(value, size_t_<1>{})) - begin;  
   if (native_offset != getter_offset) {  
        printf("native_offset != getter_offset ('%li' != '%li')\n", native_offset, getter_offset);  
	printf("FAIL\n");  
   } else {  
	printf("PASS\n");  
   }     
}  
```  
Test output is:  
```  
native_offset != getter_offset ('8' != '4')  
FAIL  
```

---

## Comment 1

> Username: varlax77  
> Created at: 2023-12-04 09:16:34 UTC  
> Url: https://github.com/boostorg/pfr/issues/153#issuecomment-1838131421  

@apolukhin  
Apparently this problem is caused by the following workaround in `detail/offset_based_getter.hpp`:  
```  
template <typename... Ts>  
struct tuple_of_aligned_storage<sequence_tuple::tuple<Ts...>> {  
  using type = sequence_tuple::tuple<internal_aligned_storage<sizeof(Ts),  
#if defined(__GNUC__) && __GNUC__ < 8 && !defined(__x86_64__) && !defined(__CYGWIN__)  
      // Before GCC-8 the `alignof` was returning the optimal alignment rather than the minimal one.  
      // We have to adjust the alignment because otherwise we get the wrong offset.  
      (alignof(Ts) > 4 ? 4 : alignof(Ts))  
#else  
      alignof(Ts)  
#endif  
  >...>;  
};  
```  
Probably the condition for workaround applicability is too wide and could be fixed with extra clause ` && !defined(__clang__)`.

---

## Comment 2

> Username: denzor200  
> Created at: 2023-12-06 00:04:25 UTC  
> Url: https://github.com/boostorg/pfr/issues/153#issuecomment-1841852089  

Guess this snipped is related to the issue: https://godbolt.org/z/7xfaKf45c

---

## Comment 3

> Username: denzor200  
> Created at: 2023-12-06 00:16:10 UTC  
> Url: https://github.com/boostorg/pfr/issues/153#issuecomment-1841863716  

@apolukhin look at these 2 lines: `(alignof(Ts) > 4 ? 4 : alignof(Ts))` and `alignof(Ts)`.  
Here we take alignment of types, but why we can't take alignment of field accessing expression?  
Sticking up this way, both lines might be simplyfied to one:  
```  
template <typename T, typename I>  
struct tuple_of_aligned_storage;  
  
template <typename... Ts, std::size_t... Is>  
struct tuple_of_aligned_storage<sequence_tuple::tuple<Ts...>, std::integer_sequence<std::size_t, Is...>> {  
  using tuple = sequence_tuple::tuple<Ts...>;  
  using type = sequence_tuple::tuple<internal_aligned_storage<sizeof(Ts),  
      sequence_tuple::get_align<Is, tuple>()  
  >...>;  
};  
```  
And what is the more important - without tricky ifdefs, nice!  
  
All what we need is to extend `sequence_tuple` adding `get_align` method:  
```  
template <std::size_t N, class T>  
constexpr auto get_align_impl(const base_from_member<N, T>& t) noexcept {  
    // NOLINTNEXTLINE(clang-analyzer-core.uninitialized.UndefReturn)  
    return std::integral_constant<std::size_t, alignof(t.value)>{};  
}  
  
template <std::size_t N, class Tuple>  
constexpr auto get_align() noexcept {  
    static_assert(N < Tuple::size_v, "====================> Boost.PFR: Tuple index out of bounds");  
    using A = decltype(sequence_tuple::get_align_impl<N>(std::declval<const Tuple&>()));  
    return A::value;  
}  
```  
  
The whole working snipped you could see here: https://godbolt.org/z/P9c3WMeha  
In my opinion it looks more reliable than the current implementation. What do you think about it, Antony?
