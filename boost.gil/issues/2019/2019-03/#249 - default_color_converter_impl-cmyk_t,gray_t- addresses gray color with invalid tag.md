# #249 - default_color_converter_impl<cmyk_t,gray_t> addresses gray color with invalid tag [Closed]

> Username: mloskot  
> Created at: 2019-03-03 01:03:55 UTC  
> Updated at: 2019-03-04 16:33:04 UTC  
> Closed at: 2019-03-04 16:33:04 UTC  
> Url: https://github.com/boostorg/gil/issues/249  

In this color converter implementation  
  
https://github.com/boostorg/gil/blob/46939c7b29cfcf84ec791ee9fb63d3916109a62b/include/boost/gil/color_convert.hpp#L195-L201  
  
The `gray_t` in `typename color_element_type<P2,gray_t>::type` should read `gray_color_t`.  
  
-----  
  
It worked by sort of an accident for the same reason this compiles:  
  
```  
// pixel element by index out of range  
using K100 = kth_semantic_element_type<gray8_pixel_t, 100>::type;  
static_assert(std::is_same<K100, unsigned char>::value, "bug or a feature");  
```  
  
### Details  
  
It works due to how MPL works in here:  
  
https://github.com/boostorg/gil/blob/6763a8cd983ae9304a77fb6bb9543996be0e4443/include/boost/gil/color_base_algorithm.hpp#L81-L87  
  
the line `mpl::at_c<typename ColorBase::layout_t::channel_mapping_t, K>::type::value;` for out of range `K=100` returns a type with `::type` member, and not a `void`, for example, what would fail the compilation (see MP11 note later).   
  
The `channel_mapping_t` is defined as `mpl::range_c`:  
  
https://github.com/boostorg/gil/blob/e3ef52bb80877168ab20746acd9aeda992d3794e/include/boost/gil/utilities.hpp#L244-L245  
  
The issue seems to be with `mpl::range_c`, as presented in this sample:  
  
```cpp  
// this fails to compile as expected  
using V = mpl::vector3_c<unsigned, 1, 2, 3>;  
using V100 = mpl::at_c<V, 100>::type;  
-------------------------------^^^^^  
  
// this compiles   
using R100 = mpl::at_c<mpl::range_c<int, 0, 1>, 100>::type;  
static_assert(std::is_same<X100, mpl::integral_c<int,100>>::value, "");  
```  
  
So, that compiles and reaches this metafunction, which effectively ignores value of `K` and that is without any `static_assert` for value of `K`:  
  
https://github.com/boostorg/gil/blob/54f1817e991c44e42c50fcbf91ad95a37f965a19/include/boost/gil/pixel.hpp#L155-L158  
  
-----  
  
The bug was found during the ongoing MPL to MP11 migration.  The `mp_at_c` returns `void` for out of range value of `K`, see [boost/mp11/algorithm.hpp:361](  
https://github.com/boostorg/mp11/blob/ed431d76248d2aa08ad81e55be314c3406abd0b1/include/boost/mp11/algorithm.hpp#L361)  
  
```cpp  
template<class L, std::size_t I>  
using mp_at_c = typename mp_if_c<(I < mp_size<L>::value), detail::mp_at_c_impl<L, I>, void>::type;  
```  
  
### Reproduce  
  
If the `kth_semantic_element_type` snippet linked above is modified with `static_assert`  
  
```  
template <typename ColorBase, int K>  
struct kth_semantic_element_type  
{  
    using channel_mapping_t = typename ColorBase::layout_t::channel_mapping_t;  
    static_assert(K < mpl::size<channel_mapping_t>::value, "K index should be less than size of channel_mapping_t sequence");  
  
    static constexpr int semantic_index = mpl::at_c<channel_mapping_t,K>::type::value;  
    using type = typename kth_element_type<ColorBase, semantic_index>::type;  
};  
```  
  
then compilation will fail with clear pointer to the `gray_t` mismatch in the `color_convert.hpp:200` displayed above:  
  
### Solution  
  
1. Fix the `gray_t` to read `gray_color_t`  
2. Spread lots of `static_assert` checking `K` based on given color space/layout.
