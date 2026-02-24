# #211 - Support multiple weights [Open]

> Username: HDembinski  
> Created at: 2019-09-11 11:11:14 UTC  
> Updated at: 2025-05-26 09:42:45 UTC  
> Url: https://github.com/boostorg/histogram/issues/211  

The current setup allows several samples to be passed to the histogram, but only one weight. Some users want to track several weights simultaneously, see  
  
https://github.com/scikit-hep/boost-histogram/issues/83  
  
This can be achieved with a new multi_weight type and some minor changes to the existing accumulators.

---

## Comment 1

> Username: zouzias  
> Created at: 2019-11-05 19:41:45 UTC  
> Url: https://github.com/boostorg/histogram/issues/211#issuecomment-549986840  

Hi @HDembinski ,  
  
Can you share an example where multiple samples are passed in the histogram? I tried the following based on the code examples on using profiles, but it does not seem to work (my knowledge in boost is limited).  
  
```cpp  
    auto p = make_profile(axis::regular<>(5, 0.0, 1.0));  
  
    /*  
      Fill profile with data, usually this happens in a loop. You pass the sample  
      with the `sample` helper function. The sample can be the first or last  
      argument.  
    */  
  
    p(0.1, sample(1., 10.));  
    p(0.15, sample(3., 30.));  
    p(0.2, sample(4., 40.));  
    p(0.9, sample(5., 50.));  
  
```  
  
Do I need to explicitly specify the boost accumulator to be used in the histogram in such a case?

---

## Comment 2

> Username: HDembinski  
> Created at: 2019-11-05 20:38:57 UTC  
> Url: https://github.com/boostorg/histogram/issues/211#issuecomment-550009706  

Hi, it doesn't work automatically with `make_profile`, you need to have an accumulator that handles more than one sample. Making accumulators is very simple, though. For a working example, scroll down to the end of in the user guide,  
https://www.boost.org/doc/libs/develop/libs/histogram/doc/html/histogram/guide.html#histogram.guide.expert.user_defined_accumulators  
or look at the example directly:  
https://github.com/boostorg/histogram/blob/develop/examples/guide_custom_accumulators_3.cpp

---

## Comment 3

> Username: HDembinski  
> Created at: 2019-11-05 20:42:58 UTC  
> Updated at: 2019-11-05 20:43:14 UTC  
> Url: https://github.com/boostorg/histogram/issues/211#issuecomment-550011140  

Note: as of today, the docs page is not up-to-date yet. The example I am referring to was just updated yesterday to show how to make accumulators that accept weights. Depending on when you look at the docs, you may see the up-to-date example that I pointed to in my second link or the older version of that example.

---

## Comment 4

> Username: zouzias  
> Created at: 2019-11-06 08:12:39 UTC  
> Url: https://github.com/boostorg/histogram/issues/211#issuecomment-550195721  

Great, many thanks @HDembinski  for sharing the links.

---

## Comment 5

> Username: HDembinski  
> Created at: 2022-09-14 14:47:21 UTC  
> Updated at: 2022-09-14 15:15:43 UTC  
> Url: https://github.com/boostorg/histogram/issues/211#issuecomment-1246880497  

Some thoughts from a Zoom dicussion with Kevin Lannon, Eddiy McGrady, Henry Schreiner and Nick Smith.  
  
Requirements:  
- The number of weights must be settable at run-time, not only at compile-time. Otherwise this feature cannot be used from Python.  
- We want the implementation to be efficient in runtime usage and memory usage.  
  
Possible implementations in C++.  Let's say we have a histogram with N cells and want to store K weights per cell. In the following I assume we want to store weights are doubles and we are on a platform where the size of a double is equal to the size of a pointer.  
  
1) Each accumulator holds a std::vector. This is simple to implement but not be optimal. The std::vector allows one to have a different number of weights per cell, although all the weights are the same. This design wastes memory since the length of the vector is stored per cell, although it only needs to be stored once per storage.  
  
This design requires at least the size for N x (K + 3) doubles in total . It is K + 3, because a std::vector at the very least contains a pointer, a size, and a capacity. The size and capacity are unsigned integers typically equal to the size of a pointer. Some implementations of std::vector use much more space than that though, for example, the std::vector of the Windows stdlib.  
  
2) Each accumulator holds a pointer to an array of dynamic size. The size of this array is not stored in the accumulator, but only once in the storage. This requires a special storage and an interplay between accumulator and storage that is currently not implemented in any of the existing accumulators and storages. In comparison to the existing accumulators, this design introduces an extra indirection every time a bin is accessed, which could be a performance penalty if the array is short. If the array is long but the number of cells is not, this could also increase performance, since the lookup of the pointer is very fast (the storage is effectively a pointer lookup table that can be stored in the L1 cache). The indirection would most likely be a cache miss, though, given the random-access nature of filling a histogram. For sufficiently small N and K, both the pointer table and the arrays will fit into the faster CPU caches, so that the cache miss is avoided. However, due to the fixed size of a cache line, this design is still inefficient unless the arrays are somehow contiguous in memory. In general they won't be.  
  
This design requires the size for N x (K + 1) + 1 doubles in total.  
  
3) We make a special storage for this case which has shape (N, K), where N is the number of cells and K is the number weights. Memory is contiguous. The accumulator in this case would be a view into the memory allocated by the storage. This may be faster if K x K is small enough for the whole histogram to fit into the faster CPU caches. If N x K is large, the histogram will not fit into the caches and a bin-lookup will most likely be a cache miss. The performance penalty of this cache miss should be the main driving cost then, making this approximately cost as much as 2).  
  
This design requires the size for N x K + 1 doubles in total, which is the lowest possible given the requirements. When N x K is large, 2) and 3) should be equally performant, but 3) should be faster if N x K is sufficiently small so that the whole histogram fits into the faster CPU caches.  
  
In summary, option 3) should be the overall fastest and is the most memory efficient.

---

## Comment 6

> Username: HDembinski  
> Created at: 2022-09-16 15:07:33 UTC  
> Updated at: 2022-09-16 15:10:35 UTC  
> Url: https://github.com/boostorg/histogram/issues/211#issuecomment-1249483294  

Working prototype of option 3:  
  
https://godbolt.org/z/x87h5MbEG  
  
```c++  
#include <boost/histogram.hpp>  
#include <boost/histogram/detail/iterator_adaptor.hpp>  
#include <boost/core/span.hpp>  
#include <memory>  
#include <algorithm>  
#include <iostream>  
  
namespace boost {  
namespace histogram {  
  
template <class T>  
struct multi_weight_value : public boost::span<T> {  
    using boost::span<T>::span;  
  
    void operator()(boost::span<T> values) {  
        if (values.size() != this->size())  
            throw std::runtime_error("size does not match");  
  
        auto it = this->begin();  
        for (const T& x : values)  
            *it++ += x;  
    }  
};  
  
  
template <class ElementType = double>  
class multi_weight {  
public:  
    using element_type = ElementType;  
    using value_type = multi_weight_value<element_type>;  
    using reference = value_type;  
    using const_reference = const value_type;  
  
    template <class T>  
    struct iterator_base : public detail::iterator_adaptor<iterator_base<T>, std::size_t, T&> {  
        using base_type =  detail::iterator_adaptor<iterator_base<T>, std::size_t, T&>;  
  
        iterator_base(multi_weight* par, std::size_t idx) : base_type{idx}, par_{par} {}  
        iterator_base(iterator_base&& other) = default;  
        iterator_base(const iterator_base& other) = default;  
  
        decltype(auto) operator*() { return T{par_->buffer_.get() + this->base() * par_->nelem_, par_->nelem_}; }  
  
        multi_weight* par_;  
    };  
  
    using iterator = iterator_base<value_type>;  
    using const_iterator = iterator_base<const value_type>;  
  
    static constexpr bool has_threading_support() { return false; }  
  
    multi_weight(const std::size_t k) : nelem_{k} {}  
  
    std::size_t size() const { return size_; }  
  
    void reset(std::size_t n) {  
        size_ = n;  
        buffer_.reset(new element_type[n * nelem_]);  
    }  
  
    iterator begin() { return {this, 0}; }  
    iterator end() { return {this, size_}; }  
  
    const_iterator begin() const { return {this, 0}; }  
    const_iterator end() const { return {this, size_}; }  
  
    reference operator[](std::size_t i) { return reference{buffer_.get() + i * nelem_, nelem_}; }  
    const_reference operator[](std::size_t i) const { return const_reference{buffer_.get() + i * nelem_, nelem_}; }  
  
    template <class T>  
    bool operator==(const multi_weight<T>& other) const {  
        if (size() != other.size())  
            return false;  
        return std::equal(buffer_._get(), buffer_.get() + size_ * nelem_, other.ptr_.get());  
    }  
  
public:  
    std::size_t size_ = 0;  
    std::size_t nelem_;  
    std::unique_ptr<element_type[]> buffer_;  
};  
  
template <class T>  
std::ostream& operator<<(std::ostream& os, const multi_weight_value<T>& v) {  
    os << "multi_weight_value(";  
    bool first = true;  
    for (const T& x : v)  
        if (first) { first = false; os << x; }  
        else os << ", " << x;  
    os << ")";  
    return os;  
}  
  
}  
}  
  
int main() {  
    using namespace boost::histogram;  
  
    using MW = multi_weight<double>;  
    MW mw(2);  
    mw.reset(2);  
    for (int i = 0; i < 4; ++i)   
        *(mw.buffer_.get() + i) = 1 + i;  
  
    for (auto it = mw.begin(); it != mw.end(); ++it)  
        std::cout << std::distance(mw.begin(), it) << " " << *it << std::endl;  
  
    auto h = make_histogram_with(multi_weight<double>(2), axis::regular(5, 0, 1));  
  
    std::vector<double> v = {{1, 2}};  
  
    h(0.1, sample(boost::span<double>(v)));  
}  
```  
  
There is still an issue in the core library, because `h(0.1, sample(v));` should also work, but currently I have to use `h(0.1, sample(boost::span<double>(v)));`

---

## Comment 7

> Username: HDembinski  
> Created at: 2022-09-19 14:30:11 UTC  
> Url: https://github.com/boostorg/histogram/issues/211#issuecomment-1251104513  

`multi_weight` should probably be renamed to `multi_storage`. It should work with the existing accumulators.

---

## Comment 8

> Username: Superharz  
> Created at: 2025-04-29 15:32:49 UTC  
> Url: https://github.com/boostorg/histogram/issues/211#issuecomment-2839360987  

Hello everybody, sorry for waking up this old issue.  
I would like to use this feature of having multiple weights per histogram since I am dealing with many systematics (O(100)) that only differ in weight. What is the status of this feature? Can the prototype be included into the library?

---

## Comment 9

> Username: HDembinski  
> Created at: 2025-04-30 12:22:20 UTC  
> Updated at: 2025-04-30 12:22:37 UTC  
> Url: https://github.com/boostorg/histogram/issues/211#issuecomment-2841801284  

Hi, there is no progress outside this issue. If you are in a hurry, you can use the code that I posted above. Full integration would require more work, serialization support, documentation, unit tests. If you are interested in submitting a PR, I'd appreciate it.

---

## Comment 10

> Username: Superharz  
> Created at: 2025-05-14 15:04:02 UTC  
> Url: https://github.com/boostorg/histogram/issues/211#issuecomment-2880588831  

Hi @HDembinski, thank you for the prototype. I can confirm that it's working.  
One (most likely naive) question: How can I use `h.fill()` with your storage?  
My approach is to do   
```  
using MW = multi_weight<double>;  
auto h  = make_histogram_with(MW(2), axis::regular(5, 0, 5));  
  
std::vector<double> v = {3,6};  
std::vector<double> v2 = {1, 2};  
auto s = boost::span<double>(v);  
auto s2 = boost::span<double>(v2);  
  
auto vars = {0.1, 1.2};  
auto weights_list = {s, s2};  
  
h.fill(vars, sample(weights_list));  
```  
however, this results in the following error log:  
  
```  
[1/2] Compiling C++ object mutliweight_test.p/multiweight_test.cxx.o  
FAILED: mutliweight_test.p/multiweight_test.cxx.o  
/cvmfs/sft.cern.ch/lcg/releases/gcc/14.2.0-2f0a0/x86_64-el9/bin/g++ -Imutliweight_test.p -I. -I.. -fdiagnostics-color=always -D_FILE_OFFSET_BITS=64 -Wall -Winvalid-pch -std=c++17 -O0 -g -MD -MQ mutliweight_test.p/multiweight_test.cxx.o -MF mutliweight_test.p/multiweight_test.cxx.o.d -o mutliweight_test.p/multiweight_test.cxx.o -c ../multiweight_test.cxx  
In file included from /cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/axes.hpp:14,  
                 from /cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/indexed.hpp:13,  
                 from /cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/algorithm/empty.hpp:11,  
                 from /cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/algorithm.hpp:15,  
                 from /cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram.hpp:27,  
                 from ../multiweight_test.cxx:1:  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/make_default.hpp: In instantiation of 'T boost::histogram::detail::make_default_impl(const T&, float) [with T = boost::histogram::multi_weight<double>]':  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/make_default.hpp:26:27:   required from 'T boost::histogram::detail::make_default(const T&) [with T = boost::histogram::multi_weight<double>]'  
   26 |   return make_default_impl(t, 0);  
      |          ~~~~~~~~~~~~~~~~~^~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/fill.hpp:88:36:   required from 'void boost::histogram::detail::storage_grower<A>::apply(S&, const boost::histogram::axis::index_type*) [with S = boost::histogram::multi_weight<double>; A = std::tuple<boost::histogram::axis::regular<double, boost::histogram::axis::transform::id, boost::histogram::axis::null_type, boost::use_default> >; boost::histogram::axis::index_type = int]'  
   88 |     auto new_storage = make_default(storage);  
      |                        ~~~~~~~~~~~~^~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/fill_n.hpp:161:12:   required from 'void boost::histogram::detail::fill_n_indices(Index*, std::size_t, std::size_t, std::size_t, S&, Axes&, const T*) [with Index = long unsigned int; S = boost::histogram::multi_weight<double>; Axes = std::tuple<boost::histogram::axis::regular<double, boost::histogram::axis::transform::id, boost::histogram::axis::null_type, boost::use_default> >; T = boost::span<const double, 18446744073709551615>; std::size_t = long unsigned int]'  
  161 |     g.apply(storage, shifts);  
      |     ~~~~~~~^~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/fill_n.hpp:224:19:   required from 'void boost::histogram::detail::fill_n_nd(std::size_t, S&, A&, std::size_t, const T*, Ts&& ...) [with Index = long unsigned int; S = boost::histogram::multi_weight<double>; A = std::tuple<boost::histogram::axis::regular<double, boost::histogram::axis::transform::id, boost::histogram::axis::null_type, boost::use_default> >; T = boost::span<const double, 18446744073709551615>; Ts = {std::pair<const boost::span<double, 18446744073709551615>*, long unsigned int>}; std::size_t = long unsigned int]'  
  224 |     fill_n_indices(indices, start, n, offset, storage, axes, values);  
      |     ~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/fill_n.hpp:236:24:   required from 'void boost::histogram::detail::fill_n_1(std::size_t, S&, std::tuple<_Elements ...>&, std::size_t, const T*, Us&& ...) [with S = boost::histogram::multi_weight<double>; As = {boost::histogram::axis::regular<double, boost::histogram::axis::transform::id, boost::histogram::axis::null_type, boost::use_default>}; T = boost::span<const double, 18446744073709551615>; Us = {std::pair<const boost::span<double, 18446744073709551615>*, long unsigned int>}; std::size_t = long unsigned int]'  
  236 |   fill_n_nd<index_type>(offset, storage, axes, vsize, values, std::forward<Us>(us)...);  
      |   ~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/fill_n.hpp:327:17:   [ skipping 3 instantiation contexts, use -ftemplate-backtrace-limit=0 to disable ]  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/fill_n.hpp:320:52:   required from 'void boost::histogram::detail::fill_n(std::true_type, std::size_t, S&, A&, boost::span<const T, N>, Us&& ...) [with S = boost::histogram::multi_weight<double>; A = std::tuple<boost::histogram::axis::regular<double, boost::histogram::axis::transform::id, boost::histogram::axis::null_type, boost::use_default> >; T = double; long unsigned int N = 18446744073709551615; Us = {std::pair<const boost::span<double, 18446744073709551615>*, long unsigned int>}; std::true_type = std::true_type; std::size_t = long unsigned int]'  
  320 |   static_if<is_convertible_to_any_value_type<A, T>>(  
      |   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^  
  321 |       [&](const auto& values, auto&&... us) {  
      |       ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  322 |         // T matches one of the axis value types, must be 1D special case  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  323 |         if (axes_rank(axes) != 1)  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~  
  324 |           BOOST_THROW_EXCEPTION(  
      |           ~~~~~~~~~~~~~~~~~~~~~~  
  325 |               std::invalid_argument("number of arguments must match histogram rank"));  
      |               ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  326 |         fill_n_check_extra_args(values.size(), std::forward<Us>(us)...);  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  327 |         fill_n_1(offset, storage, axes, values.size(), &values, std::forward<Us>(us)...);  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  328 |       },  
      |       ~~  
  329 |       [&](const auto& values, auto&&... us) {  
      |       ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  330 |         // generic ND case  
      |         ~~~~~~~~~~~~~~~~~~  
  331 |         if (axes_rank(axes) != values.size())  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  332 |           BOOST_THROW_EXCEPTION(  
      |           ~~~~~~~~~~~~~~~~~~~~~~  
  333 |               std::invalid_argument("number of arguments must match histogram rank"));  
      |               ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  334 |         const auto vsize = get_total_size(axes, values);  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  335 |         fill_n_check_extra_args(vsize, std::forward<Us>(us)...);  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  336 |         fill_n_1(offset, storage, axes, vsize, values.data(), std::forward<Us>(us)...);  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  337 |       },  
      |       ~~  
  338 |       values, std::forward<Us>(us)...);  
      |       ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/histogram.hpp:290:25:   required from 'boost::histogram::histogram<std::tuple<boost::histogram::axis::regular<double, boost::histogram::axis::transform::id, boost::histogram::axis::null_type, boost::use_default> >, boost::histogram::multi_weight<double> >::fill<std::initializer_list<double>, std::initializer_list<boost::span<double, 18446744073709551615> >&>(const std::initializer_list<double>&, const boost::histogram::sample_type<std::tuple<std::initializer_list<boost::span<double, 18446744073709551615> >&> >&)::<lambda(const auto:98& ...)> [with auto:98 = {std::initializer_list<boost::span<double, 18446744073709551615> >}]'  
  290 |           detail::fill_n(mp11::mp_bool<(sample_valid)>{}, offset_, storage_, axes_,  
      |           ~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  291 |                          make_span(args), detail::to_ptr_size(sargs)...);  
      |                          ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/mp11/tuple.hpp:37:36:   required by substitution of 'template<class F, class Tp, long unsigned int ...J> constexpr decltype (forward<F>(f)((get<J>)((forward<Tp>)(boost::mp11::detail::tuple_apply_impl::tp))...)) boost::mp11::detail::tuple_apply_impl(F&&, Tp&&, boost::mp11::integer_sequence<long unsigned int, J ...>) [with F = boost::histogram::histogram<std::tuple<boost::histogram::axis::regular<double, boost::histogram::axis::transform::id, boost::histogram::axis::null_type, boost::use_default> >, boost::histogram::multi_weight<double> >::fill<std::initializer_list<double>, std::initializer_list<boost::span<double, 18446744073709551615> >&>(const std::initializer_list<double>&, const boost::histogram::sample_type<std::tuple<std::initializer_list<boost::span<double, 18446744073709551615> >&> >&)::<lambda(const auto:98& ...)>; Tp = const std::tuple<std::initializer_list<boost::span<double, 18446744073709551615> >&>&; long unsigned int ...J = {0}]'  
   37 |     -> decltype( std::forward<F>(f)( get<J>(std::forward<Tp>(tp))... ) )  
      |                  ~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/mp11/tuple.hpp:47:42:   required by substitution of 'template<class F, class Tp, class Seq> constexpr decltype (boost::mp11::detail::tuple_apply_impl(forward<F>(f), forward<Tp>(tp), Seq())) boost::mp11::tuple_apply(F&&, Tp&&) [with F = boost::histogram::histogram<std::tuple<boost::histogram::axis::regular<double, boost::histogram::axis::transform::id, boost::histogram::axis::null_type, boost::use_default> >, boost::histogram::multi_weight<double> >::fill<std::initializer_list<double>, std::initializer_list<boost::span<double, 18446744073709551615> >&>(const std::initializer_list<double>&, const boost::histogram::sample_type<std::tuple<std::initializer_list<boost::span<double, 18446744073709551615> >&> >&)::<lambda(const auto:98& ...)>; Tp = const std::tuple<std::initializer_list<boost::span<double, 18446744073709551615> >&>&; Seq = boost::mp11::integer_sequence<long unsigned int, 0>]'  
   47 |     -> decltype( detail::tuple_apply_impl( std::forward<F>(f), std::forward<Tp>(tp), Seq() ) )  
      |                  ~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/histogram.hpp:286:22:   required from 'void boost::histogram::histogram<Axes, Storage>::fill(const Iterable&, const boost::histogram::sample_type<std::tuple<_Args2 ...> >&) [with Iterable = std::initializer_list<double>; Ts = {std::initializer_list<boost::span<double, 18446744073709551615> >&}; <template-parameter-2-3> = boost::histogram::detail::requires_iterable<std::initializer_list<double>, void>; Axes = std::tuple<boost::histogram::axis::regular<double, boost::histogram::axis::transform::id, boost::histogram::axis::null_type, boost::use_default> >; Storage = boost::histogram::multi_weight<double>]'  
  286 |     mp11::tuple_apply( // LCOV_EXCL_LINE: gcc-11 is missing this line for no reason  
      |     ~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  287 |         [&](const auto&... sargs) {  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  288 |           constexpr bool sample_valid =  
      |           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  289 |               std::is_convertible<sample_args_passed, typename acc_traits::args>::value;  
      |               ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  290 |           detail::fill_n(mp11::mp_bool<(sample_valid)>{}, offset_, storage_, axes_,  
      |           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  291 |                          make_span(args), detail::to_ptr_size(sargs)...);  
      |                          ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  292 |         },  
      |         ~~  
  293 |         samples.value);  
      |         ~~~~~~~~~~~~~~  
../multiweight_test.cxx:123:11:   required from here  
  123 |     h.fill(vars, sample(weights_list));  
      |     ~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/make_default.hpp:21:12: error: no matching function for call to 'boost::histogram::multi_weight<double>::multi_weight(<brace-enclosed initializer list>)'  
   21 |   return T{};  
      |            ^  
../multiweight_test.cxx:52:5: note: candidate: 'boost::histogram::multi_weight<ElementType>::multi_weight(std::size_t) [with ElementType = double; std::size_t = long unsigned int]'  
   52 |     multi_weight(const std::size_t k) : nelem_{k} {}  
      |     ^~~~~~~~~~~~  
../multiweight_test.cxx:52:5: note:   candidate expects 1 argument, 0 provided  
../multiweight_test.cxx:27:7: note: candidate: 'boost::histogram::multi_weight<double>::multi_weight(boost::histogram::multi_weight<double>&&)'  
   27 | class multi_weight {  
      |       ^~~~~~~~~~~~  
../multiweight_test.cxx:27:7: note:   candidate expects 1 argument, 0 provided  
ninja: build stopped: subcommand failed.  
```  
  
Could you please tell me what I am doing wrong?

---

## Comment 11

> Username: HDembinski  
> Created at: 2025-05-14 17:14:17 UTC  
> Url: https://github.com/boostorg/histogram/issues/211#issuecomment-2880970260  

.fill won't work out of the box, I guess. In my example I used the direct call, where you have to write the fill loop yourself.

---

## Comment 12

> Username: HDembinski  
> Created at: 2025-05-14 17:16:37 UTC  
> Url: https://github.com/boostorg/histogram/issues/211#issuecomment-2880976377  

Although, try using a span when you pass the sample, like I did.

---

## Comment 13

> Username: Superharz  
> Created at: 2025-05-14 18:06:47 UTC  
> Updated at: 2025-05-14 18:08:45 UTC  
> Url: https://github.com/boostorg/histogram/issues/211#issuecomment-2881100149  

Thank you @HDembinski for your reply.  
  
With this setup:  
```  
std::vector<double> v = {3,6};  
std::vector<double> v2 = {1, 2};  
std::vector<std::vector<double>> v3 = {v, v2};  
  
auto s = boost::span<double>(v);  
auto s2 = boost::span<double>(v2);  
std::vector<boost::span<double>> weights_list = {s,s2};  
auto s3 = boost::span<boost::span<double>>(weights_list);  
boost::span<std::vector<double>> sv3 = boost::span<std::vector<double>>(v3);  
```  
  
I tested the following combinations:  
1. `h.fill(vars, sample(v3));`  
2. `h.fill(vars, sample(weights_list));`  
3. `h.fill(vars, sample(s3));`  
4. `h.fill(vars, sample(sv3));`  
  
  
The 2. and 3. case result in the error message of my previous reply. 1. case has a different error message, but also does not use an `boost::span`.  
  
The 4. version results in a different error message:  
  
```  
[1/2] Compiling C++ object mutliweight_test.p/multiweight_test.cxx.o  
FAILED: mutliweight_test.p/multiweight_test.cxx.o   
/cvmfs/sft.cern.ch/lcg/releases/gcc/14.2.0-2f0a0/x86_64-el9/bin/g++ -Imutliweight_test.p -I. -I.. -fdiagnostics-color=always -D_FILE_OFFSET_BITS=64 -Wall -Winvalid-pch -std=c++17 -O0 -g -MD -MQ mutliweight_test.p/multiweight_test.cxx.o -MF mutliweight_test.p/multiweight_test.cxx.o.d -o mutliweight_test.p/multiweight_test.cxx.o -c ../multiweight_test.cxx  
../multiweight_test.cxx: In function 'int main()':  
../multiweight_test.cxx:125:10: warning: variable 's3' set but not used [-Wunused-but-set-variable]  
  125 |     auto s3 = boost::span<boost::span<double>>(weights_list);  
      |          ^~  
In file included from /cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/histogram.hpp:15,  
                 from /cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/algorithm/project.hpp:15,  
                 from /cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/algorithm.hpp:16,  
                 from /cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram.hpp:27,  
                 from ../multiweight_test.cxx:1:  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/fill.hpp: In instantiation of 'void boost::histogram::detail::fill_storage_element(T&&, const Us& ...) [with T = boost::histogram::multi_weight_value<double>; Us = {std::vector<double, std::allocator<double> >}]':  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/fill_n.hpp:169:25:   required from 'void boost::histogram::detail::fill_n_storage(S&, Index, Ts&& ...) [with S = boost::histogram::multi_weight<double>; Index = long unsigned int; Ts = {std::pair<std::vector<double, std::allocator<double> >*, long unsigned int>}]'  
  169 |     fill_storage_element(s[idx], *p.first...);  
      |     ~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/fill_n.hpp:227:21:   required from 'void boost::histogram::detail::fill_n_nd(std::size_t, S&, A&, std::size_t, const T*, Ts&& ...) [with Index = long unsigned int; S = boost::histogram::multi_weight<double>; A = std::tuple<boost::histogram::axis::regular<double, boost::histogram::axis::transform::id, boost::histogram::axis::null_type, boost::use_default> >; T = boost::span<const double, 18446744073709551615>; Ts = {std::pair<std::vector<double, std::allocator<double> >*, long unsigned int>}; std::size_t = long unsigned int]'  
  227 |       fill_n_storage(storage, idx, std::forward<Ts>(ts)...);  
      |       ~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/fill_n.hpp:236:24:   required from 'void boost::histogram::detail::fill_n_1(std::size_t, S&, std::tuple<_Elements ...>&, std::size_t, const T*, Us&& ...) [with S = boost::histogram::multi_weight<double>; As = {boost::histogram::axis::regular<double, boost::histogram::axis::transform::id, boost::histogram::axis::null_type, boost::use_default>}; T = boost::span<const double, 18446744073709551615>; Us = {std::pair<std::vector<double, std::allocator<double> >*, long unsigned int>}; std::size_t = long unsigned int]'  
  236 |   fill_n_nd<index_type>(offset, storage, axes, vsize, values, std::forward<Us>(us)...);  
      |   ~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/fill_n.hpp:327:17:   required from 'boost::histogram::detail::fill_n<boost::histogram::multi_weight<double>, std::tuple<boost::histogram::axis::regular<double, boost::histogram::axis::transform::id, boost::histogram::axis::null_type, boost::use_default> >, double, 18446744073709551615, std::pair<std::vector<double, std::allocator<double> >*, long unsigned int> >(std::true_type, std::size_t, boost::histogram::multi_weight<double>&, std::tuple<boost::histogram::axis::regular<double, boost::histogram::axis::transform::id, boost::histogram::axis::null_type, boost::use_default> >&, boost::span<const double, 18446744073709551615>, std::pair<std::vector<double, std::allocator<double> >*, long unsigned int>&&)::<lambda(const auto:84&, auto:85&& ...)> [with auto:84 = boost::span<const double, 18446744073709551615>; auto:85 = {std::pair<std::vector<double, std::allocator<double> >*, long unsigned int>}]'  
  327 |         fill_n_1(offset, storage, axes, values.size(), &values, std::forward<Us>(us)...);  
      |         ~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/static_if.hpp:20:56:   [ skipping 2 instantiation contexts, use -ftemplate-backtrace-limit=0 to disable ]  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/fill_n.hpp:320:52:   required from 'void boost::histogram::detail::fill_n(std::true_type, std::size_t, S&, A&, boost::span<const T, N>, Us&& ...) [with S = boost::histogram::multi_weight<double>; A = std::tuple<boost::histogram::axis::regular<double, boost::histogram::axis::transform::id, boost::histogram::axis::null_type, boost::use_default> >; T = double; long unsigned int N = 18446744073709551615; Us = {std::pair<std::vector<double, std::allocator<double> >*, long unsigned int>}; std::true_type = std::true_type; std::size_t = long unsigned int]'  
  320 |   static_if<is_convertible_to_any_value_type<A, T>>(  
      |   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^  
  321 |       [&](const auto& values, auto&&... us) {  
      |       ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~         
  322 |         // T matches one of the axis value types, must be 1D special case  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  323 |         if (axes_rank(axes) != 1)  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~                     
  324 |           BOOST_THROW_EXCEPTION(  
      |           ~~~~~~~~~~~~~~~~~~~~~~                      
  325 |               std::invalid_argument("number of arguments must match histogram rank"));  
      |               ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  326 |         fill_n_check_extra_args(values.size(), std::forward<Us>(us)...);  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  327 |         fill_n_1(offset, storage, axes, values.size(), &values, std::forward<Us>(us)...);  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  328 |       },  
      |       ~~                                              
  329 |       [&](const auto& values, auto&&... us) {  
      |       ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~         
  330 |         // generic ND case  
      |         ~~~~~~~~~~~~~~~~~~                            
  331 |         if (axes_rank(axes) != values.size())  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~         
  332 |           BOOST_THROW_EXCEPTION(  
      |           ~~~~~~~~~~~~~~~~~~~~~~                      
  333 |               std::invalid_argument("number of arguments must match histogram rank"));  
      |               ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  334 |         const auto vsize = get_total_size(axes, values);  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  335 |         fill_n_check_extra_args(vsize, std::forward<Us>(us)...);  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  336 |         fill_n_1(offset, storage, axes, vsize, values.data(), std::forward<Us>(us)...);  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  337 |       },  
      |       ~~                                              
  338 |       values, std::forward<Us>(us)...);  
      |       ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~                
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/histogram.hpp:290:25:   required from 'boost::histogram::histogram<std::tuple<boost::histogram::axis::regular<double, boost::histogram::axis::transform::id, boost::histogram::axis::null_type, boost::use_default> >, boost::histogram::multi_weight<double> >::fill<std::initializer_list<double>, boost::span<std::vector<double, std::allocator<double> >, 18446744073709551615>&>(const std::initializer_list<double>&, const boost::histogram::sample_type<std::tuple<boost::span<std::vector<double, std::allocator<double> >, 18446744073709551615>&> >&)::<lambda(const auto:98& ...)> [with auto:98 = {boost::span<std::vector<double, std::allocator<double> >, 18446744073709551615>}]'  
  290 |           detail::fill_n(mp11::mp_bool<(sample_valid)>{}, offset_, storage_, axes_,  
      |           ~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  291 |                          make_span(args), detail::to_ptr_size(sargs)...);  
      |                          ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/mp11/tuple.hpp:37:36:   required by substitution of 'template<class F, class Tp, long unsigned int ...J> constexpr decltype (forward<F>(f)((get<J>)((forward<Tp>)(boost::mp11::detail::tuple_apply_impl::tp))...)) boost::mp11::detail::tuple_apply_impl(F&&, Tp&&, boost::mp11::integer_sequence<long unsigned int, J ...>) [with F = boost::histogram::histogram<std::tuple<boost::histogram::axis::regular<double, boost::histogram::axis::transform::id, boost::histogram::axis::null_type, boost::use_default> >, boost::histogram::multi_weight<double> >::fill<std::initializer_list<double>, boost::span<std::vector<double, std::allocator<double> >, 18446744073709551615>&>(const std::initializer_list<double>&, const boost::histogram::sample_type<std::tuple<boost::span<std::vector<double, std::allocator<double> >, 18446744073709551615>&> >&)::<lambda(const auto:98& ...)>; Tp = const std::tuple<boost::span<std::vector<double, std::allocator<double> >, 18446744073709551615>&>&; long unsigned int ...J = {0}]'  
   37 |     -> decltype( std::forward<F>(f)( get<J>(std::forward<Tp>(tp))... ) )  
      |                  ~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/mp11/tuple.hpp:47:42:   required by substitution of 'template<class F, class Tp, class Seq> constexpr decltype (boost::mp11::detail::tuple_apply_impl(forward<F>(f), forward<Tp>(tp), Seq())) boost::mp11::tuple_apply(F&&, Tp&&) [with F = boost::histogram::histogram<std::tuple<boost::histogram::axis::regular<double, boost::histogram::axis::transform::id, boost::histogram::axis::null_type, boost::use_default> >, boost::histogram::multi_weight<double> >::fill<std::initializer_list<double>, boost::span<std::vector<double, std::allocator<double> >, 18446744073709551615>&>(const std::initializer_list<double>&, const boost::histogram::sample_type<std::tuple<boost::span<std::vector<double, std::allocator<double> >, 18446744073709551615>&> >&)::<lambda(const auto:98& ...)>; Tp = const std::tuple<boost::span<std::vector<double, std::allocator<double> >, 18446744073709551615>&>&; Seq = boost::mp11::integer_sequence<long unsigned int, 0>]'  
   47 |     -> decltype( detail::tuple_apply_impl( std::forward<F>(f), std::forward<Tp>(tp), Seq() ) )  
      |                  ~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/histogram.hpp:286:22:   required from 'void boost::histogram::histogram<Axes, Storage>::fill(const Iterable&, const boost::histogram::sample_type<std::tuple<_Args2 ...> >&) [with Iterable = std::initializer_list<double>; Ts = {boost::span<std::vector<double, std::allocator<double> >, 18446744073709551615>&}; <template-parameter-2-3> = boost::histogram::detail::requires_iterable<std::initializer_list<double>, void>; Axes = std::tuple<boost::histogram::axis::regular<double, boost::histogram::axis::transform::id, boost::histogram::axis::null_type, boost::use_default> >; Storage = boost::histogram::multi_weight<double>]'  
  286 |     mp11::tuple_apply( // LCOV_EXCL_LINE: gcc-11 is missing this line for no reason  
      |     ~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  287 |         [&](const auto&... sargs) {  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  288 |           constexpr bool sample_valid =  
      |           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  289 |               std::is_convertible<sample_args_passed, typename acc_traits::args>::value;  
      |               ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  290 |           detail::fill_n(mp11::mp_bool<(sample_valid)>{}, offset_, storage_, axes_,  
      |           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  291 |                          make_span(args), detail::to_ptr_size(sargs)...);  
      |                          ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  292 |         },  
      |         ~~              
  293 |         samples.value);  
      |         ~~~~~~~~~~~~~~  
../multiweight_test.cxx:126:11:   required from here  
  126 |     h.fill(vars, sample(sv3));  
      |     ~~~~~~^~~~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/fill.hpp:162:28: error: no matching function for call to 'fill_storage_element_impl(boost::histogram::detail::priority<2>, boost::histogram::multi_weight_value<double>, const std::vector<double, std::allocator<double> >&)'  
  162 |   fill_storage_element_impl(priority<2>{}, std::forward<T>(t), args...);  
      |   ~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/fill.hpp:137:6: note: candidate: 'template<class T, class ... Us> decltype ((t(boost::histogram::detail::fill_storage_element_impl::args ...), void())) boost::histogram::detail::fill_storage_element_impl(priority<2>, T&&, const Us& ...)'  
  137 | auto fill_storage_element_impl(priority<2>, T&& t, const Us&... args) noexcept  
      |      ^~~~~~~~~~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/fill.hpp:137:6: note:   template argument deduction/substitution failed:  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/fill.hpp: In substitution of 'template<class T, class ... Us> decltype ((t(boost::histogram::detail::fill_storage_element_impl::args ...), void())) boost::histogram::detail::fill_storage_element_impl(priority<2>, T&&, const Us& ...) [with T = boost::histogram::multi_weight_value<double>; Us = {std::vector<double, std::allocator<double> >}]':  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/fill.hpp:162:28:   required from 'void boost::histogram::detail::fill_storage_element(T&&, const Us& ...) [with T = boost::histogram::multi_weight_value<double>; Us = {std::vector<double, std::allocator<double> >}]'  
  162 |   fill_storage_element_impl(priority<2>{}, std::forward<T>(t), args...);  
      |   ~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/fill_n.hpp:169:25:   required from 'void boost::histogram::detail::fill_n_storage(S&, Index, Ts&& ...) [with S = boost::histogram::multi_weight<double>; Index = long unsigned int; Ts = {std::pair<std::vector<double, std::allocator<double> >*, long unsigned int>}]'  
  169 |     fill_storage_element(s[idx], *p.first...);  
      |     ~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/fill_n.hpp:227:21:   required from 'void boost::histogram::detail::fill_n_nd(std::size_t, S&, A&, std::size_t, const T*, Ts&& ...) [with Index = long unsigned int; S = boost::histogram::multi_weight<double>; A = std::tuple<boost::histogram::axis::regular<double, boost::histogram::axis::transform::id, boost::histogram::axis::null_type, boost::use_default> >; T = boost::span<const double, 18446744073709551615>; Ts = {std::pair<std::vector<double, std::allocator<double> >*, long unsigned int>}; std::size_t = long unsigned int]'  
  227 |       fill_n_storage(storage, idx, std::forward<Ts>(ts)...);  
      |       ~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/fill_n.hpp:236:24:   required from 'void boost::histogram::detail::fill_n_1(std::size_t, S&, std::tuple<_Elements ...>&, std::size_t, const T*, Us&& ...) [with S = boost::histogram::multi_weight<double>; As = {boost::histogram::axis::regular<double, boost::histogram::axis::transform::id, boost::histogram::axis::null_type, boost::use_default>}; T = boost::span<const double, 18446744073709551615>; Us = {std::pair<std::vector<double, std::allocator<double> >*, long unsigned int>}; std::size_t = long unsigned int]'  
  236 |   fill_n_nd<index_type>(offset, storage, axes, vsize, values, std::forward<Us>(us)...);  
      |   ~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/fill_n.hpp:327:17:   required from 'boost::histogram::detail::fill_n<boost::histogram::multi_weight<double>, std::tuple<boost::histogram::axis::regular<double, boost::histogram::axis::transform::id, boost::histogram::axis::null_type, boost::use_default> >, double, 18446744073709551615, std::pair<std::vector<double, std::allocator<double> >*, long unsigned int> >(std::true_type, std::size_t, boost::histogram::multi_weight<double>&, std::tuple<boost::histogram::axis::regular<double, boost::histogram::axis::transform::id, boost::histogram::axis::null_type, boost::use_default> >&, boost::span<const double, 18446744073709551615>, std::pair<std::vector<double, std::allocator<double> >*, long unsigned int>&&)::<lambda(const auto:84&, auto:85&& ...)> [with auto:84 = boost::span<const double, 18446744073709551615>; auto:85 = {std::pair<std::vector<double, std::allocator<double> >*, long unsigned int>}]'  
  327 |         fill_n_1(offset, storage, axes, values.size(), &values, std::forward<Us>(us)...);  
      |         ~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/static_if.hpp:20:56:   [ skipping 2 instantiation contexts, use -ftemplate-backtrace-limit=0 to disable ]  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/fill_n.hpp:320:52:   required from 'void boost::histogram::detail::fill_n(std::true_type, std::size_t, S&, A&, boost::span<const T, N>, Us&& ...) [with S = boost::histogram::multi_weight<double>; A = std::tuple<boost::histogram::axis::regular<double, boost::histogram::axis::transform::id, boost::histogram::axis::null_type, boost::use_default> >; T = double; long unsigned int N = 18446744073709551615; Us = {std::pair<std::vector<double, std::allocator<double> >*, long unsigned int>}; std::true_type = std::true_type; std::size_t = long unsigned int]'  
  320 |   static_if<is_convertible_to_any_value_type<A, T>>(  
      |   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^  
  321 |       [&](const auto& values, auto&&... us) {  
      |       ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~         
  322 |         // T matches one of the axis value types, must be 1D special case  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  323 |         if (axes_rank(axes) != 1)  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~                     
  324 |           BOOST_THROW_EXCEPTION(  
      |           ~~~~~~~~~~~~~~~~~~~~~~                      
  325 |               std::invalid_argument("number of arguments must match histogram rank"));  
      |               ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  326 |         fill_n_check_extra_args(values.size(), std::forward<Us>(us)...);  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  327 |         fill_n_1(offset, storage, axes, values.size(), &values, std::forward<Us>(us)...);  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  328 |       },  
      |       ~~                                              
  329 |       [&](const auto& values, auto&&... us) {  
      |       ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~         
  330 |         // generic ND case  
      |         ~~~~~~~~~~~~~~~~~~                            
  331 |         if (axes_rank(axes) != values.size())  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~         
  332 |           BOOST_THROW_EXCEPTION(  
      |           ~~~~~~~~~~~~~~~~~~~~~~                      
  333 |               std::invalid_argument("number of arguments must match histogram rank"));  
      |               ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  334 |         const auto vsize = get_total_size(axes, values);  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  335 |         fill_n_check_extra_args(vsize, std::forward<Us>(us)...);  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  336 |         fill_n_1(offset, storage, axes, vsize, values.data(), std::forward<Us>(us)...);  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  337 |       },  
      |       ~~                                              
  338 |       values, std::forward<Us>(us)...);  
      |       ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~                
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/histogram.hpp:290:25:   required from 'boost::histogram::histogram<std::tuple<boost::histogram::axis::regular<double, boost::histogram::axis::transform::id, boost::histogram::axis::null_type, boost::use_default> >, boost::histogram::multi_weight<double> >::fill<std::initializer_list<double>, boost::span<std::vector<double, std::allocator<double> >, 18446744073709551615>&>(const std::initializer_list<double>&, const boost::histogram::sample_type<std::tuple<boost::span<std::vector<double, std::allocator<double> >, 18446744073709551615>&> >&)::<lambda(const auto:98& ...)> [with auto:98 = {boost::span<std::vector<double, std::allocator<double> >, 18446744073709551615>}]'  
  290 |           detail::fill_n(mp11::mp_bool<(sample_valid)>{}, offset_, storage_, axes_,  
      |           ~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  291 |                          make_span(args), detail::to_ptr_size(sargs)...);  
      |                          ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/mp11/tuple.hpp:37:36:   required by substitution of 'template<class F, class Tp, long unsigned int ...J> constexpr decltype (forward<F>(f)((get<J>)((forward<Tp>)(boost::mp11::detail::tuple_apply_impl::tp))...)) boost::mp11::detail::tuple_apply_impl(F&&, Tp&&, boost::mp11::integer_sequence<long unsigned int, J ...>) [with F = boost::histogram::histogram<std::tuple<boost::histogram::axis::regular<double, boost::histogram::axis::transform::id, boost::histogram::axis::null_type, boost::use_default> >, boost::histogram::multi_weight<double> >::fill<std::initializer_list<double>, boost::span<std::vector<double, std::allocator<double> >, 18446744073709551615>&>(const std::initializer_list<double>&, const boost::histogram::sample_type<std::tuple<boost::span<std::vector<double, std::allocator<double> >, 18446744073709551615>&> >&)::<lambda(const auto:98& ...)>; Tp = const std::tuple<boost::span<std::vector<double, std::allocator<double> >, 18446744073709551615>&>&; long unsigned int ...J = {0}]'  
   37 |     -> decltype( std::forward<F>(f)( get<J>(std::forward<Tp>(tp))... ) )  
      |                  ~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/mp11/tuple.hpp:47:42:   required by substitution of 'template<class F, class Tp, class Seq> constexpr decltype (boost::mp11::detail::tuple_apply_impl(forward<F>(f), forward<Tp>(tp), Seq())) boost::mp11::tuple_apply(F&&, Tp&&) [with F = boost::histogram::histogram<std::tuple<boost::histogram::axis::regular<double, boost::histogram::axis::transform::id, boost::histogram::axis::null_type, boost::use_default> >, boost::histogram::multi_weight<double> >::fill<std::initializer_list<double>, boost::span<std::vector<double, std::allocator<double> >, 18446744073709551615>&>(const std::initializer_list<double>&, const boost::histogram::sample_type<std::tuple<boost::span<std::vector<double, std::allocator<double> >, 18446744073709551615>&> >&)::<lambda(const auto:98& ...)>; Tp = const std::tuple<boost::span<std::vector<double, std::allocator<double> >, 18446744073709551615>&>&; Seq = boost::mp11::integer_sequence<long unsigned int, 0>]'  
   47 |     -> decltype( detail::tuple_apply_impl( std::forward<F>(f), std::forward<Tp>(tp), Seq() ) )  
      |                  ~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/histogram.hpp:286:22:   required from 'void boost::histogram::histogram<Axes, Storage>::fill(const Iterable&, const boost::histogram::sample_type<std::tuple<_Args2 ...> >&) [with Iterable = std::initializer_list<double>; Ts = {boost::span<std::vector<double, std::allocator<double> >, 18446744073709551615>&}; <template-parameter-2-3> = boost::histogram::detail::requires_iterable<std::initializer_list<double>, void>; Axes = std::tuple<boost::histogram::axis::regular<double, boost::histogram::axis::transform::id, boost::histogram::axis::null_type, boost::use_default> >; Storage = boost::histogram::multi_weight<double>]'  
  286 |     mp11::tuple_apply( // LCOV_EXCL_LINE: gcc-11 is missing this line for no reason  
      |     ~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  287 |         [&](const auto&... sargs) {  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  288 |           constexpr bool sample_valid =  
      |           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  289 |               std::is_convertible<sample_args_passed, typename acc_traits::args>::value;  
      |               ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  290 |           detail::fill_n(mp11::mp_bool<(sample_valid)>{}, offset_, storage_, axes_,  
      |           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  291 |                          make_span(args), detail::to_ptr_size(sargs)...);  
      |                          ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  292 |         },  
      |         ~~              
  293 |         samples.value);  
      |         ~~~~~~~~~~~~~~  
../multiweight_test.cxx:126:11:   required from here  
  126 |     h.fill(vars, sample(sv3));  
      |     ~~~~~~^~~~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/fill.hpp:138:18: error: no match for call to '(boost::histogram::multi_weight_value<double>) (const std::vector<double, std::allocator<double> >&)'  
  138 |     -> decltype(t(args...), void()) {  
      |                 ~^~~~~~~~~  
../multiweight_test.cxx:15:10: note: candidate: 'void boost::histogram::multi_weight_value<T>::operator()(boost::span<T, 18446744073709551615>) [with T = double]'  
   15 |     void operator()(boost::span<T> values) {  
      |          ^~~~~~~~  
../multiweight_test.cxx:15:36: note:   no known conversion for argument 1 from 'const std::vector<double, std::allocator<double> >' to 'boost::span<double, 18446744073709551615>'  
   15 |     void operator()(boost::span<T> values) {  
      |                     ~~~~~~~~~~~~~~~^~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/fill.hpp: In instantiation of 'void boost::histogram::detail::fill_storage_element(T&&, const Us& ...) [with T = boost::histogram::multi_weight_value<double>; Us = {std::vector<double, std::allocator<double> >}]':  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/fill_n.hpp:169:25:   required from 'void boost::histogram::detail::fill_n_storage(S&, Index, Ts&& ...) [with S = boost::histogram::multi_weight<double>; Index = long unsigned int; Ts = {std::pair<std::vector<double, std::allocator<double> >*, long unsigned int>}]'  
  169 |     fill_storage_element(s[idx], *p.first...);  
      |     ~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/fill_n.hpp:227:21:   required from 'void boost::histogram::detail::fill_n_nd(std::size_t, S&, A&, std::size_t, const T*, Ts&& ...) [with Index = long unsigned int; S = boost::histogram::multi_weight<double>; A = std::tuple<boost::histogram::axis::regular<double, boost::histogram::axis::transform::id, boost::histogram::axis::null_type, boost::use_default> >; T = boost::span<const double, 18446744073709551615>; Ts = {std::pair<std::vector<double, std::allocator<double> >*, long unsigned int>}; std::size_t = long unsigned int]'  
  227 |       fill_n_storage(storage, idx, std::forward<Ts>(ts)...);  
      |       ~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/fill_n.hpp:236:24:   required from 'void boost::histogram::detail::fill_n_1(std::size_t, S&, std::tuple<_Elements ...>&, std::size_t, const T*, Us&& ...) [with S = boost::histogram::multi_weight<double>; As = {boost::histogram::axis::regular<double, boost::histogram::axis::transform::id, boost::histogram::axis::null_type, boost::use_default>}; T = boost::span<const double, 18446744073709551615>; Us = {std::pair<std::vector<double, std::allocator<double> >*, long unsigned int>}; std::size_t = long unsigned int]'  
  236 |   fill_n_nd<index_type>(offset, storage, axes, vsize, values, std::forward<Us>(us)...);  
      |   ~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/fill_n.hpp:327:17:   required from 'boost::histogram::detail::fill_n<boost::histogram::multi_weight<double>, std::tuple<boost::histogram::axis::regular<double, boost::histogram::axis::transform::id, boost::histogram::axis::null_type, boost::use_default> >, double, 18446744073709551615, std::pair<std::vector<double, std::allocator<double> >*, long unsigned int> >(std::true_type, std::size_t, boost::histogram::multi_weight<double>&, std::tuple<boost::histogram::axis::regular<double, boost::histogram::axis::transform::id, boost::histogram::axis::null_type, boost::use_default> >&, boost::span<const double, 18446744073709551615>, std::pair<std::vector<double, std::allocator<double> >*, long unsigned int>&&)::<lambda(const auto:84&, auto:85&& ...)> [with auto:84 = boost::span<const double, 18446744073709551615>; auto:85 = {std::pair<std::vector<double, std::allocator<double> >*, long unsigned int>}]'  
  327 |         fill_n_1(offset, storage, axes, values.size(), &values, std::forward<Us>(us)...);  
      |         ~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/static_if.hpp:20:56:   [ skipping 2 instantiation contexts, use -ftemplate-backtrace-limit=0 to disable ]  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/fill_n.hpp:320:52:   required from 'void boost::histogram::detail::fill_n(std::true_type, std::size_t, S&, A&, boost::span<const T, N>, Us&& ...) [with S = boost::histogram::multi_weight<double>; A = std::tuple<boost::histogram::axis::regular<double, boost::histogram::axis::transform::id, boost::histogram::axis::null_type, boost::use_default> >; T = double; long unsigned int N = 18446744073709551615; Us = {std::pair<std::vector<double, std::allocator<double> >*, long unsigned int>}; std::true_type = std::true_type; std::size_t = long unsigned int]'  
  320 |   static_if<is_convertible_to_any_value_type<A, T>>(  
      |   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^  
  321 |       [&](const auto& values, auto&&... us) {  
      |       ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~         
  322 |         // T matches one of the axis value types, must be 1D special case  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  323 |         if (axes_rank(axes) != 1)  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~                     
  324 |           BOOST_THROW_EXCEPTION(  
      |           ~~~~~~~~~~~~~~~~~~~~~~                      
  325 |               std::invalid_argument("number of arguments must match histogram rank"));  
      |               ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  326 |         fill_n_check_extra_args(values.size(), std::forward<Us>(us)...);  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  327 |         fill_n_1(offset, storage, axes, values.size(), &values, std::forward<Us>(us)...);  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  328 |       },  
      |       ~~                                              
  329 |       [&](const auto& values, auto&&... us) {  
      |       ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~         
  330 |         // generic ND case  
      |         ~~~~~~~~~~~~~~~~~~                            
  331 |         if (axes_rank(axes) != values.size())  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~         
  332 |           BOOST_THROW_EXCEPTION(  
      |           ~~~~~~~~~~~~~~~~~~~~~~                      
  333 |               std::invalid_argument("number of arguments must match histogram rank"));  
      |               ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  334 |         const auto vsize = get_total_size(axes, values);  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  335 |         fill_n_check_extra_args(vsize, std::forward<Us>(us)...);  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  336 |         fill_n_1(offset, storage, axes, vsize, values.data(), std::forward<Us>(us)...);  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  337 |       },  
      |       ~~                                              
  338 |       values, std::forward<Us>(us)...);  
      |       ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~                
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/histogram.hpp:290:25:   required from 'boost::histogram::histogram<std::tuple<boost::histogram::axis::regular<double, boost::histogram::axis::transform::id, boost::histogram::axis::null_type, boost::use_default> >, boost::histogram::multi_weight<double> >::fill<std::initializer_list<double>, boost::span<std::vector<double, std::allocator<double> >, 18446744073709551615>&>(const std::initializer_list<double>&, const boost::histogram::sample_type<std::tuple<boost::span<std::vector<double, std::allocator<double> >, 18446744073709551615>&> >&)::<lambda(const auto:98& ...)> [with auto:98 = {boost::span<std::vector<double, std::allocator<double> >, 18446744073709551615>}]'  
  290 |           detail::fill_n(mp11::mp_bool<(sample_valid)>{}, offset_, storage_, axes_,  
      |           ~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  291 |                          make_span(args), detail::to_ptr_size(sargs)...);  
      |                          ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/mp11/tuple.hpp:37:36:   required by substitution of 'template<class F, class Tp, long unsigned int ...J> constexpr decltype (forward<F>(f)((get<J>)((forward<Tp>)(boost::mp11::detail::tuple_apply_impl::tp))...)) boost::mp11::detail::tuple_apply_impl(F&&, Tp&&, boost::mp11::integer_sequence<long unsigned int, J ...>) [with F = boost::histogram::histogram<std::tuple<boost::histogram::axis::regular<double, boost::histogram::axis::transform::id, boost::histogram::axis::null_type, boost::use_default> >, boost::histogram::multi_weight<double> >::fill<std::initializer_list<double>, boost::span<std::vector<double, std::allocator<double> >, 18446744073709551615>&>(const std::initializer_list<double>&, const boost::histogram::sample_type<std::tuple<boost::span<std::vector<double, std::allocator<double> >, 18446744073709551615>&> >&)::<lambda(const auto:98& ...)>; Tp = const std::tuple<boost::span<std::vector<double, std::allocator<double> >, 18446744073709551615>&>&; long unsigned int ...J = {0}]'  
   37 |     -> decltype( std::forward<F>(f)( get<J>(std::forward<Tp>(tp))... ) )  
      |                  ~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/mp11/tuple.hpp:47:42:   required by substitution of 'template<class F, class Tp, class Seq> constexpr decltype (boost::mp11::detail::tuple_apply_impl(forward<F>(f), forward<Tp>(tp), Seq())) boost::mp11::tuple_apply(F&&, Tp&&) [with F = boost::histogram::histogram<std::tuple<boost::histogram::axis::regular<double, boost::histogram::axis::transform::id, boost::histogram::axis::null_type, boost::use_default> >, boost::histogram::multi_weight<double> >::fill<std::initializer_list<double>, boost::span<std::vector<double, std::allocator<double> >, 18446744073709551615>&>(const std::initializer_list<double>&, const boost::histogram::sample_type<std::tuple<boost::span<std::vector<double, std::allocator<double> >, 18446744073709551615>&> >&)::<lambda(const auto:98& ...)>; Tp = const std::tuple<boost::span<std::vector<double, std::allocator<double> >, 18446744073709551615>&>&; Seq = boost::mp11::integer_sequence<long unsigned int, 0>]'  
   47 |     -> decltype( detail::tuple_apply_impl( std::forward<F>(f), std::forward<Tp>(tp), Seq() ) )  
      |                  ~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/histogram.hpp:286:22:   required from 'void boost::histogram::histogram<Axes, Storage>::fill(const Iterable&, const boost::histogram::sample_type<std::tuple<_Args2 ...> >&) [with Iterable = std::initializer_list<double>; Ts = {boost::span<std::vector<double, std::allocator<double> >, 18446744073709551615>&}; <template-parameter-2-3> = boost::histogram::detail::requires_iterable<std::initializer_list<double>, void>; Axes = std::tuple<boost::histogram::axis::regular<double, boost::histogram::axis::transform::id, boost::histogram::axis::null_type, boost::use_default> >; Storage = boost::histogram::multi_weight<double>]'  
  286 |     mp11::tuple_apply( // LCOV_EXCL_LINE: gcc-11 is missing this line for no reason  
      |     ~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  287 |         [&](const auto&... sargs) {  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  288 |           constexpr bool sample_valid =  
      |           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  289 |               std::is_convertible<sample_args_passed, typename acc_traits::args>::value;  
      |               ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  290 |           detail::fill_n(mp11::mp_bool<(sample_valid)>{}, offset_, storage_, axes_,  
      |           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  291 |                          make_span(args), detail::to_ptr_size(sargs)...);  
      |                          ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  292 |         },  
      |         ~~              
  293 |         samples.value);  
      |         ~~~~~~~~~~~~~~  
../multiweight_test.cxx:126:11:   required from here  
  126 |     h.fill(vars, sample(sv3));  
      |     ~~~~~~^~~~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/fill.hpp:143:6: note: candidate: 'template<class T, class U> decltype ((boost::histogram::detail::fill_storage_element_impl::t += boost::histogram::detail::fill_storage_element_impl::w, void())) boost::histogram::detail::fill_storage_element_impl(priority<1>, T&&, const boost::histogram::weight_type<U>&)'  
  143 | auto fill_storage_element_impl(priority<1>, T&& t, const weight_type<U>& w) noexcept  
      |      ^~~~~~~~~~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/fill.hpp:143:6: note:   template argument deduction/substitution failed:  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/fill.hpp:162:28: note:   'const std::vector<double, std::allocator<double> >' is not derived from 'const boost::histogram::weight_type<U>'  
  162 |   fill_storage_element_impl(priority<2>{}, std::forward<T>(t), args...);  
      |   ~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/fill.hpp:150:6: note: candidate: 'template<class T, class U> decltype ((boost::histogram::detail::fill_storage_element_impl::t += boost::histogram::detail::fill_storage_element_impl::w->value, void())) boost::histogram::detail::fill_storage_element_impl(priority<0>, T&&, const boost::histogram::weight_type<U>&)'  
  150 | auto fill_storage_element_impl(priority<0>, T&& t, const weight_type<U>& w) noexcept  
      |      ^~~~~~~~~~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/fill.hpp:150:6: note:   template argument deduction/substitution failed:  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/fill.hpp:162:28: note:   'const std::vector<double, std::allocator<double> >' is not derived from 'const boost::histogram::weight_type<U>'  
  162 |   fill_storage_element_impl(priority<2>{}, std::forward<T>(t), args...);  
      |   ~~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/fill.hpp:156:6: note: candidate: 'template<class T> decltype (((++ t), void())) boost::histogram::detail::fill_storage_element_impl(priority<1>, T&&)'  
  156 | auto fill_storage_element_impl(priority<1>, T&& t) noexcept -> decltype(++t, void()) {  
      |      ^~~~~~~~~~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/fill.hpp:156:6: note:   candidate expects 2 arguments, 3 provided  
In file included from /cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/axes.hpp:14,  
                 from /cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/indexed.hpp:13,  
                 from /cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/algorithm/empty.hpp:11,  
                 from /cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/algorithm.hpp:15:  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/make_default.hpp: In instantiation of 'T boost::histogram::detail::make_default_impl(const T&, float) [with T = boost::histogram::multi_weight<double>]':  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/make_default.hpp:26:27:   required from 'T boost::histogram::detail::make_default(const T&) [with T = boost::histogram::multi_weight<double>]'  
   26 |   return make_default_impl(t, 0);  
      |          ~~~~~~~~~~~~~~~~~^~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/fill.hpp:88:36:   required from 'void boost::histogram::detail::storage_grower<A>::apply(S&, const boost::histogram::axis::index_type*) [with S = boost::histogram::multi_weight<double>; A = std::tuple<boost::histogram::axis::regular<double, boost::histogram::axis::transform::id, boost::histogram::axis::null_type, boost::use_default> >; boost::histogram::axis::index_type = int]'  
   88 |     auto new_storage = make_default(storage);  
      |                        ~~~~~~~~~~~~^~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/fill_n.hpp:161:12:   required from 'void boost::histogram::detail::fill_n_indices(Index*, std::size_t, std::size_t, std::size_t, S&, Axes&, const T*) [with Index = long unsigned int; S = boost::histogram::multi_weight<double>; Axes = std::tuple<boost::histogram::axis::regular<double, boost::histogram::axis::transform::id, boost::histogram::axis::null_type, boost::use_default> >; T = boost::span<const double, 18446744073709551615>; std::size_t = long unsigned int]'  
  161 |     g.apply(storage, shifts);  
      |     ~~~~~~~^~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/fill_n.hpp:224:19:   required from 'void boost::histogram::detail::fill_n_nd(std::size_t, S&, A&, std::size_t, const T*, Ts&& ...) [with Index = long unsigned int; S = boost::histogram::multi_weight<double>; A = std::tuple<boost::histogram::axis::regular<double, boost::histogram::axis::transform::id, boost::histogram::axis::null_type, boost::use_default> >; T = boost::span<const double, 18446744073709551615>; Ts = {std::pair<std::vector<double, std::allocator<double> >*, long unsigned int>}; std::size_t = long unsigned int]'  
  224 |     fill_n_indices(indices, start, n, offset, storage, axes, values);  
      |     ~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/fill_n.hpp:236:24:   required from 'void boost::histogram::detail::fill_n_1(std::size_t, S&, std::tuple<_Elements ...>&, std::size_t, const T*, Us&& ...) [with S = boost::histogram::multi_weight<double>; As = {boost::histogram::axis::regular<double, boost::histogram::axis::transform::id, boost::histogram::axis::null_type, boost::use_default>}; T = boost::span<const double, 18446744073709551615>; Us = {std::pair<std::vector<double, std::allocator<double> >*, long unsigned int>}; std::size_t = long unsigned int]'  
  236 |   fill_n_nd<index_type>(offset, storage, axes, vsize, values, std::forward<Us>(us)...);  
      |   ~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/fill_n.hpp:327:17:   [ skipping 3 instantiation contexts, use -ftemplate-backtrace-limit=0 to disable ]  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/fill_n.hpp:320:52:   required from 'void boost::histogram::detail::fill_n(std::true_type, std::size_t, S&, A&, boost::span<const T, N>, Us&& ...) [with S = boost::histogram::multi_weight<double>; A = std::tuple<boost::histogram::axis::regular<double, boost::histogram::axis::transform::id, boost::histogram::axis::null_type, boost::use_default> >; T = double; long unsigned int N = 18446744073709551615; Us = {std::pair<std::vector<double, std::allocator<double> >*, long unsigned int>}; std::true_type = std::true_type; std::size_t = long unsigned int]'  
  320 |   static_if<is_convertible_to_any_value_type<A, T>>(  
      |   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^  
  321 |       [&](const auto& values, auto&&... us) {  
      |       ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~         
  322 |         // T matches one of the axis value types, must be 1D special case  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  323 |         if (axes_rank(axes) != 1)  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~                     
  324 |           BOOST_THROW_EXCEPTION(  
      |           ~~~~~~~~~~~~~~~~~~~~~~                      
  325 |               std::invalid_argument("number of arguments must match histogram rank"));  
      |               ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  326 |         fill_n_check_extra_args(values.size(), std::forward<Us>(us)...);  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  327 |         fill_n_1(offset, storage, axes, values.size(), &values, std::forward<Us>(us)...);  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  328 |       },  
      |       ~~                                              
  329 |       [&](const auto& values, auto&&... us) {  
      |       ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~         
  330 |         // generic ND case  
      |         ~~~~~~~~~~~~~~~~~~                            
  331 |         if (axes_rank(axes) != values.size())  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~         
  332 |           BOOST_THROW_EXCEPTION(  
      |           ~~~~~~~~~~~~~~~~~~~~~~                      
  333 |               std::invalid_argument("number of arguments must match histogram rank"));  
      |               ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  334 |         const auto vsize = get_total_size(axes, values);  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  335 |         fill_n_check_extra_args(vsize, std::forward<Us>(us)...);  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  336 |         fill_n_1(offset, storage, axes, vsize, values.data(), std::forward<Us>(us)...);  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  337 |       },  
      |       ~~                                              
  338 |       values, std::forward<Us>(us)...);  
      |       ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~                
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/histogram.hpp:290:25:   required from 'boost::histogram::histogram<std::tuple<boost::histogram::axis::regular<double, boost::histogram::axis::transform::id, boost::histogram::axis::null_type, boost::use_default> >, boost::histogram::multi_weight<double> >::fill<std::initializer_list<double>, boost::span<std::vector<double, std::allocator<double> >, 18446744073709551615>&>(const std::initializer_list<double>&, const boost::histogram::sample_type<std::tuple<boost::span<std::vector<double, std::allocator<double> >, 18446744073709551615>&> >&)::<lambda(const auto:98& ...)> [with auto:98 = {boost::span<std::vector<double, std::allocator<double> >, 18446744073709551615>}]'  
  290 |           detail::fill_n(mp11::mp_bool<(sample_valid)>{}, offset_, storage_, axes_,  
      |           ~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  291 |                          make_span(args), detail::to_ptr_size(sargs)...);  
      |                          ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/mp11/tuple.hpp:37:36:   required by substitution of 'template<class F, class Tp, long unsigned int ...J> constexpr decltype (forward<F>(f)((get<J>)((forward<Tp>)(boost::mp11::detail::tuple_apply_impl::tp))...)) boost::mp11::detail::tuple_apply_impl(F&&, Tp&&, boost::mp11::integer_sequence<long unsigned int, J ...>) [with F = boost::histogram::histogram<std::tuple<boost::histogram::axis::regular<double, boost::histogram::axis::transform::id, boost::histogram::axis::null_type, boost::use_default> >, boost::histogram::multi_weight<double> >::fill<std::initializer_list<double>, boost::span<std::vector<double, std::allocator<double> >, 18446744073709551615>&>(const std::initializer_list<double>&, const boost::histogram::sample_type<std::tuple<boost::span<std::vector<double, std::allocator<double> >, 18446744073709551615>&> >&)::<lambda(const auto:98& ...)>; Tp = const std::tuple<boost::span<std::vector<double, std::allocator<double> >, 18446744073709551615>&>&; long unsigned int ...J = {0}]'  
   37 |     -> decltype( std::forward<F>(f)( get<J>(std::forward<Tp>(tp))... ) )  
      |                  ~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/mp11/tuple.hpp:47:42:   required by substitution of 'template<class F, class Tp, class Seq> constexpr decltype (boost::mp11::detail::tuple_apply_impl(forward<F>(f), forward<Tp>(tp), Seq())) boost::mp11::tuple_apply(F&&, Tp&&) [with F = boost::histogram::histogram<std::tuple<boost::histogram::axis::regular<double, boost::histogram::axis::transform::id, boost::histogram::axis::null_type, boost::use_default> >, boost::histogram::multi_weight<double> >::fill<std::initializer_list<double>, boost::span<std::vector<double, std::allocator<double> >, 18446744073709551615>&>(const std::initializer_list<double>&, const boost::histogram::sample_type<std::tuple<boost::span<std::vector<double, std::allocator<double> >, 18446744073709551615>&> >&)::<lambda(const auto:98& ...)>; Tp = const std::tuple<boost::span<std::vector<double, std::allocator<double> >, 18446744073709551615>&>&; Seq = boost::mp11::integer_sequence<long unsigned int, 0>]'  
   47 |     -> decltype( detail::tuple_apply_impl( std::forward<F>(f), std::forward<Tp>(tp), Seq() ) )  
      |                  ~~~~~~~~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/histogram.hpp:286:22:   required from 'void boost::histogram::histogram<Axes, Storage>::fill(const Iterable&, const boost::histogram::sample_type<std::tuple<_Args2 ...> >&) [with Iterable = std::initializer_list<double>; Ts = {boost::span<std::vector<double, std::allocator<double> >, 18446744073709551615>&}; <template-parameter-2-3> = boost::histogram::detail::requires_iterable<std::initializer_list<double>, void>; Axes = std::tuple<boost::histogram::axis::regular<double, boost::histogram::axis::transform::id, boost::histogram::axis::null_type, boost::use_default> >; Storage = boost::histogram::multi_weight<double>]'  
  286 |     mp11::tuple_apply( // LCOV_EXCL_LINE: gcc-11 is missing this line for no reason  
      |     ~~~~~~~~~~~~~~~~~^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  287 |         [&](const auto&... sargs) {  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  288 |           constexpr bool sample_valid =  
      |           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  289 |               std::is_convertible<sample_args_passed, typename acc_traits::args>::value;  
      |               ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  290 |           detail::fill_n(mp11::mp_bool<(sample_valid)>{}, offset_, storage_, axes_,  
      |           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  291 |                          make_span(args), detail::to_ptr_size(sargs)...);  
      |                          ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  292 |         },  
      |         ~~              
  293 |         samples.value);  
      |         ~~~~~~~~~~~~~~  
../multiweight_test.cxx:126:11:   required from here  
  126 |     h.fill(vars, sample(sv3));  
      |     ~~~~~~^~~~~~~~~~~~~~~~~~~  
/cvmfs/sft.cern.ch/lcg/views/LCG_107a/x86_64-el9-gcc14-opt/include/boost/histogram/detail/make_default.hpp:21:12: error: no matching function for call to 'boost::histogram::multi_weight<double>::multi_weight(<brace-enclosed initializer list>)'  
   21 |   return T{};  
      |            ^  
../multiweight_test.cxx:52:5: note: candidate: 'boost::histogram::multi_weight<ElementType>::multi_weight(std::size_t) [with ElementType = double; std::size_t = long unsigned int]'  
   52 |     multi_weight(const std::size_t k) : nelem_{k} {}  
      |     ^~~~~~~~~~~~  
../multiweight_test.cxx:52:5: note:   candidate expects 1 argument, 0 provided  
../multiweight_test.cxx:27:7: note: candidate: 'boost::histogram::multi_weight<double>::multi_weight(boost::histogram::multi_weight<double>&&)'  
   27 | class multi_weight {  
      |       ^~~~~~~~~~~~  
../multiweight_test.cxx:27:7: note:   candidate expects 1 argument, 0 provided  
ninja: build stopped: subcommand failed.  
```

---

## Comment 14

> Username: HDembinski  
> Created at: 2025-05-15 07:28:12 UTC  
> Url: https://github.com/boostorg/histogram/issues/211#issuecomment-2882840525  

Ok, then the logic inside .fill (which is a template-based meta-program, hence the long and confusing error message) needs to be adapted to this use case. I will try to look into this over the weekend, but I suggest you use a manual loop like in my example  
  
```cpp  
std::vector<double> w = {{1, 2}, {3, 4}, {5, 6}}; // your weights (pair of 2 in this case)  
std::vector<double> x = {0.5, 1.1, 2.3}; // your x values  
  
for (int i = 0; i < x.size(); ++i)   
    h(x[i], sample(boost::span<double>(w[i])));  
```

---

## Comment 15

> Username: Superharz  
> Created at: 2025-05-15 10:23:41 UTC  
> Url: https://github.com/boostorg/histogram/issues/211#issuecomment-2883322592  

Hi @HDembinski, thank you for that code snippet.  
It looks like I also found a workaround (but I don't want to stop you from finding a more sustainable solution):  
The problem apparently was, that the constructor of the `multi_weight` storage takes 1 argument (the number of weights), but boost histogram tries to use the constructor without any arguments during the `.fill()` function (however, I couldn't find the place in the core code where this is happening).   
Therefore, I gave the `multi_weight` constructor a default argument:  
  
Old:  
`multi_weight(const std::size_t k) : nelem_{k} {}`  
  
New:  
`multi_weight(const std::size_t k = 0) : nelem_{k} {}`  
  
Now, the `h.fill(vars, sample(std::vector<boost::span<double>>))` works without complaints.

---

## Comment 16

> Username: HDembinski  
> Created at: 2025-05-19 16:01:37 UTC  
> Url: https://github.com/boostorg/histogram/issues/211#issuecomment-2891545628  

Ah, that's really interesting. I am glad it works for you now. I am somewhat motivated to add multi_weight to the library proper. It's a really nice feature and there is obviously sustained interest in it.

---

## Comment 17

> Username: Superharz  
> Created at: 2025-05-19 16:08:35 UTC  
> Url: https://github.com/boostorg/histogram/issues/211#issuecomment-2891566751  

Hi @HDembinski, yes it kind of works now.  
I am trying right now to make it usable with the python boost-histogram bindings (because I want to use it in python at the end).  
For this, I had to make some more modifications to your prototype (serialization now also works, but I don't know if I chose a good implementation):  
  
```c++  
#ifndef BOOST_HISTOGRAM_MULTI_WEIGHT_HPP  
#define BOOST_HISTOGRAM_MULTI_WEIGHT_HPP  
  
#include <boost/core/nvp.hpp>  
#include <boost/histogram.hpp>  
#include <boost/histogram/detail/iterator_adaptor.hpp>  
#include <boost/histogram/detail/array_wrapper.hpp>  
#include <boost/serialization/unique_ptr.hpp>  
#include <boost/core/span.hpp>  
#include <memory>  
#include <algorithm>  
#include <iostream>  
  
namespace boost {  
namespace histogram {  
  
template <class T>  
struct multi_weight_value : public boost::span<T> {  
    using boost::span<T>::span;  
  
    void operator()(boost::span<T> values) {  
        if (values.size() != this->size())  
            throw std::runtime_error("size does not match");  
        auto it = this->begin();  
        for (const T& x : values)  
            *it++ += x;  
    }  
  
    bool operator==(const boost::span<T> values) const {  
        if (values.size() != this->size())  
            return false;  
  
        return std::equal(this->begin(), this->end(), values.begin());  
    }  
    bool operator!=(const boost::span<T> values) const {return !operator==(values);}  
    void operator+=(const boost::span<T> values) {operator()(values);}  
};  
  
  
template <class ElementType = double>  
class multi_weight {  
public:  
    using element_type = ElementType;  
    using value_type = multi_weight_value<element_type>;  
    using reference = value_type;  
    using const_reference = const value_type&;  
  
    template <class Value, class Reference, class MWPtr>  
    struct iterator_base : public detail::iterator_adaptor<iterator_base<Value, Reference, MWPtr>, std::size_t, Reference> {  
        using base_type =  detail::iterator_adaptor<iterator_base<Value, Reference, MWPtr>, std::size_t, Reference>;  
  
        iterator_base() = default;  
        iterator_base(const iterator_base& other) : iterator_base(other.par_, other.base()) {}  
        iterator_base(MWPtr par, std::size_t idx) : base_type{idx}, par_{par} {}  
  
        auto operator*() const { return Reference{par_->buffer_.get() + this->base() * par_->nelem_, par_->nelem_}; }  
  
        MWPtr par_ = nullptr;  
    };  
  
    using iterator = iterator_base<value_type, reference, multi_weight*>;  
    using const_iterator = iterator_base<const value_type, const_reference, const multi_weight*>;  
  
    static constexpr bool has_threading_support() { return false; }  
  
    multi_weight(const std::size_t k = 0) : nelem_{k} {}  
  
    multi_weight(const multi_weight& other) : nelem_{other.nelem_} {  
        reset(other.size_);  
        std::copy(other.buffer_.get(), other.buffer_.get() + buffer_length_, buffer_.get());  
    }  
  
    multi_weight& operator=(const multi_weight& other) {  
        nelem_ = other.nelem_;  
        reset(other.size_);  
        std::copy(other.buffer_.get(), other.buffer_.get() + buffer_length_, buffer_.get());  
        return *this;  
    }  
  
  
    std::size_t size() const { return size_; }  
  
    void reset(std::size_t n) {  
        size_ = n;  
        buffer_length_ = n * nelem_;  
        buffer_.reset(new element_type[buffer_length_]);  
    }  
  
    iterator begin() { return {this, 0}; }  
    iterator end() { return {this, size_}; }  
  
    const_iterator begin() const { return {this, 0}; }  
    const_iterator end() const { return {this, size_}; }  
  
    auto operator[](std::size_t i) { return reference{buffer_.get() + i * nelem_, nelem_}; }  
    auto operator[](std::size_t i) const { return const_reference{buffer_.get() + i * nelem_, nelem_}; }  
  
    template <class T>  
    bool operator==(const multi_weight<T>& other) const {  
        if (size() != other.size())  
            return false;  
        return std::equal(buffer_.get(), buffer_.get() + buffer_length_, other.buffer_.get());  
    }  
  
    template <class T>  
    bool operator!=(const multi_weight<T>& other) const { return !operator==(other); }  
  
    template <class T>  
    void operator+=(const multi_weight<T>& other) {  
        if (buffer_length_ != other.buffer_length_)  
            throw std::runtime_error("size does not match");  
        for (std::size_t i = 0; i < buffer_length_; i++) {  
            buffer_[i] += other.buffer_[i];  
        }  
    }  
  
  
    template <class Archive>  
    void serialize(Archive& ar, unsigned /* version */) {  
        ar& make_nvp("size", size_);  
        ar& make_nvp("nelem", nelem_);  
        ar& make_nvp("buffer_length", buffer_length_);  
        std::vector<element_type> w;  
        if (Archive::is_loading::value)  
        {  
            ar& make_nvp("buffer", w);  
            reset(size_);  
            std::swap_ranges(buffer_.get(), buffer_.get() + buffer_length_, w.data());  
        } else {  
            w.assign(buffer_.get(), buffer_.get() + buffer_length_);  
            ar& make_nvp("buffer", w);  
        }  
    }  
  
public:  
    std::size_t size_ = 0;  
    std::size_t nelem_ = 0;  
    std::size_t buffer_length_ = 0;  
    std::unique_ptr<element_type[]> buffer_;  
};  
  
template <class T>  
std::ostream& operator<<(std::ostream& os, const multi_weight_value<T>& v) {  
    os << "multi_weight_value(";  
    bool first = true;  
    for (const T& x : v)  
        if (first) { first = false; os << x; }  
        else os << ", " << x;  
    os << ")";  
    return os;  
}  
  
template <class T>  
std::ostream& operator<<(std::ostream& os, const multi_weight<T>& v) {  
    os << "multi_weight(\n";  
    int index = 0;  
    for (const multi_weight_value<T>& x : v) {  
        os << "Index " << index << ": " << x << "\n";  
        index++;  
    }  
    os << ")";  
    return os;  
}  
  
  
} // namespace histogram  
} // namespace boost  
  
#endif  
  
```

---

## Comment 18

> Username: Superharz  
> Created at: 2025-05-19 16:19:53 UTC  
> Url: https://github.com/boostorg/histogram/issues/211#issuecomment-2891595503  

On the python side, I have still to figure out how to implement the buffer for this storage and how to actually use it at run time in a convenient way.  
It definitely would be favorable if this could become part of the official library and if this could also get official python binding (this  is of course the wrong place to ask this, but the second can only happen after the first).

---

## Comment 19

> Username: Superharz  
> Created at: 2025-05-26 09:42:44 UTC  
> Url: https://github.com/boostorg/histogram/issues/211#issuecomment-2909088299  

I have opened the draft PR #411 to track my current implementation of @HDembinski prototype.
