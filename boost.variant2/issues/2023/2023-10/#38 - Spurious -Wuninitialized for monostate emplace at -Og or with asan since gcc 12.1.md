# #38 - Spurious -Wuninitialized for monostate emplace at -Og or with asan since gcc 12.1 [Closed]

> Username: ecatmur  
> Created at: 2023-10-23 20:41:07 UTC  
> Updated at: 2023-10-24 14:22:37 UTC  
> Closed at: 2023-10-24 09:42:12 UTC  
> Url: https://github.com/boostorg/variant2/issues/38  

Upstream: https://gcc.gnu.org/bugzilla/show_bug.cgi?id=111944  
  
```c++  
#include <boost/variant2/variant.hpp>  
int main() {  
    boost::variant2::variant<boost::variant2::monostate, int> r;  
    r.emplace<boost::variant2::monostate>();  
}  
```  
  
gcc 12.1+, -Og or -O -fsanitize=address, -Wuninitialized:  
  
```  
In member function 'constexpr void boost::variant2::detail::variant_storage_impl<std::integral_constant<bool, true>, T1, T ...>::emplace_impl(boost::mp11::mp_true, boost::mp11::mp_size_t<I>, A&& ...) [with long unsigned int I = 1; A = {}; T1 = boost::variant2::detail::none; T = {boost::variant2::monostate, int}]',  
    inlined from 'constexpr void boost::variant2::detail::variant_storage_impl<std::integral_constant<bool, true>, T1, T ...>::emplace(boost::mp11::mp_size_t<I>, A&& ...) [with long unsigned int I = 1; A = {}; T1 = boost::variant2::detail::none; T = {boost::variant2::monostate, int}]' at boost/variant2/variant.hpp:705:27,  
    inlined from 'constexpr void boost::variant2::detail::variant_base_impl<true, true, T ...>::emplace_impl(boost::mp11::mp_true, A&& ...) [with long unsigned int J = 1; U = boost::variant2::monostate; A = {}; T = {boost::variant2::monostate, int}]' at boost/variant2/variant.hpp:902:20,  
    inlined from 'constexpr void boost::variant2::detail::variant_base_impl<true, true, T ...>::emplace(A&& ...) [with long unsigned int I = 0; A = {}; T = {boost::variant2::monostate, int}]' at boost/variant2/variant.hpp:921:33,  
    inlined from 'constexpr U& boost::variant2::variant<T>::emplace(A&& ...) [with U = boost::variant2::monostate; A = {}; E = void; T = {boost::variant2::monostate, int}]' at boost/variant2/variant.hpp:1690:49:  
boost/variant2/variant.hpp:696:9: warning: '<anonymous>' is used uninitialized [-Wuninitialized]  
  696 |         *this = variant_storage_impl( mp11::mp_size_t<I>(), std::forward<A>(a)... );  
      |         ^  
boost/variant2/variant.hpp: In member function 'constexpr U& boost::variant2::variant<T>::emplace(A&& ...) [with U = boost::variant2::monostate; A = {}; E = void; T = {boost::variant2::monostate, int}]':  
boost/variant2/variant.hpp:696:17: note: '<anonymous>' declared here  
  696 |         *this = variant_storage_impl( mp11::mp_size_t<I>(), std::forward<A>(a)... );  
      |                 ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
```  
  
This affects Boost.System `result<void>` (e.g. test/result_emplace.cpp).  
  
You already have a suppression for -Wmaybe-uninitialized, do you want to add one for -Wuninitialized as well?

---

## Comment 1

> Username: pdimov  
> Created at: 2023-10-23 22:42:42 UTC  
> Updated at: 2023-10-24 09:42:43 UTC  
> Url: https://github.com/boostorg/variant2/issues/38#issuecomment-1776136634  

I wondered why my ASAN job doesn't exhibit this warning in variant_emplace_index.cpp. Turns out that it's -DNDEBUG that suppresses it for some reason. (https://godbolt.org/z/GT3aMqKxz vs https://godbolt.org/z/vd1fYrvfK.)

---

## Comment 2

> Username: pdimov  
> Created at: 2023-10-23 22:46:36 UTC  
> Url: https://github.com/boostorg/variant2/issues/38#issuecomment-1776140590  

I assume #39 was made in response to this issue?

---

## Comment 3

> Username: 13steinj  
> Created at: 2023-10-24 14:22:37 UTC  
> Url: https://github.com/boostorg/variant2/issues/38#issuecomment-1777326106  

> I assume #39 was made in response to this issue?  
  
Yes
