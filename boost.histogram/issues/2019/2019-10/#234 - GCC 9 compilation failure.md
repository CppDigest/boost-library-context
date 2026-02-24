# #234 - GCC 9 compilation failure [Closed]

> Username: henryiii  
> Created at: 2019-10-10 02:21:27 UTC  
> Updated at: 2019-10-11 20:31:33 UTC  
> Closed at: 2019-10-10 09:07:19 UTC  
> Url: https://github.com/boostorg/histogram/issues/234  

On my mac (and on a linux machine in docker `gcc:9`), GCC 9 fails to compile after the recent updates:  
  
```  
The following tests FAILED:  
	 80 - compile-BoostHistogram-histogram_fill_test_cpp (Failed)  
	 81 - run-BoostHistogram-histogram_fill_test_cpp (Not Run)  
```  
  
The compile output is too long for github in all it's templated glory, so trimmed version below:  
  
```  
 80/104 Test  #80: compile-BoostHistogram-histogram_fill_test_cpp .............***Failed    6.13 sec  
Building CXX object test/CMakeFiles/BoostHistogram-histogram_fill_test_cpp.dir/histogram_fill_test.cpp.o  
In file included from /Users/henryiii/git/software/histogram-python/extern/histogram/include/boost/histogram/histogram.hpp:15,  
                 from /Users/henryiii/git/software/histogram-python/extern/histogram/test/histogram_fill_test.cpp:14:  
/Users/henryiii/git/software/histogram-python/extern/histogram/include/boost/histogram/detail/fill_n.hpp: In instantiation of 'void boost::histogram::detail::fill_n(std::size_t, S&, A&, boost::histogram::detail::span<const T>, Us&& ...) [with S = boost::histogram::unlimited_storage<>; A = std::tuple<boost::histogram::axis::integer<int, boost::histogram::axis::null_type, boost::use_default> >; T = boost::variant2::variant<int, std::vector<int, std::allocator<int> > >; Us = {std::pair<const double*, long unsigned int>}; std::size_t = long unsigned int]':  
/Users/henryiii/git/software/histogram-python/extern/histogram/include/boost/histogram/histogram.hpp:232:19:   required from 'void boost::histogram::histogram<Axes, Storage>::fill(const Iterable&, const boost::histogram::weight_type<T>&) [with Iterable = std::vector<boost::variant2::variant<int, std::vector<int, std::allocator<int> > > >; T = std::vector<double, std::allocator<double> >&; <template-parameter-2-3> = boost::histogram::detail::requires_iterable<std::vector<boost::variant2::variant<int, std::vector<int, std::allocator<int> > > >, void>; Axes = std::tuple<boost::histogram::axis::integer<int, boost::histogram::axis::null_type, boost::use_default> >; Storage = boost::histogram::unlimited_storage<>]'  
/Users/henryiii/git/software/histogram-python/extern/histogram/test/histogram_fill_test.cpp:132:5:   required from 'void run_tests(const std::vector<int>&, const std::vector<int>&, const std::vector<double, std::allocator<double> >&) [with Tag = std::integral_constant<bool, false>]'  
/Users/henryiii/git/software/histogram-python/extern/histogram/test/histogram_fill_test.cpp:290:32:   required from here  
/Users/henryiii/git/software/histogram-python/extern/histogram/include/boost/histogram/detail/fill_n.hpp:277:82: error: redeclaration of 'std::pair<const double*, long unsigned int>& us#0'  
  277 |         fill_n_1(offset, storage, axes, values.size(), &values, std::forward<Us>(us)...);  
      |                                                                                  ^~  
/Users/henryiii/git/software/histogram-python/extern/histogram/include/boost/histogram/detail/fill_n.hpp:276:65: note: 'std::pair<const double*, long unsigned int>& us#0' previously declared here  
  276 |         fill_n_check_extra_args(values.size(), std::forward<Us>(us)...);  
      |                                                                 ^~  
/Users/henryiii/git/software/histogram-python/extern/histogram/include/boost/histogram/detail/fill_n.hpp:272:7: error: member 'boost::histogram::detail::fill_n(std::size_t, S&, A&, boost::histogram::detail::span<const T>, Us&& ...) [with S = boost::histogram::unlimited_storage<>; A = std::tuple<boost::histogram::axis::integer<int, boost::histogram::axis::null_type, boost::use_default> >; T = boost::variant2::variant<int, std::vector<int, std::allocator<int> > >; Us = {std::pair<const double*, long unsigned int>}; std::size_t = long unsigned int]::<lambda(const auto:93&)>::<us#0 capture>' is uninitialized reference  
  272 |       [&](const auto& values) {  
      |       ^~~~~~~~~~~~~~~~~~~~~~~~~  
  273 |         if (axes_rank(axes) != 1)  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~  
  274 |           BOOST_THROW_EXCEPTION(  
      |           ~~~~~~~~~~~~~~~~~~~~~~  
  275 |               std::invalid_argument("number of arguments must match histogram rank"));  
      |               ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  276 |         fill_n_check_extra_args(values.size(), std::forward<Us>(us)...);  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  277 |         fill_n_1(offset, storage, axes, values.size(), &values, std::forward<Us>(us)...);  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  278 |       },  
      |       ~  
/Users/henryiii/git/software/histogram-python/extern/histogram/include/boost/histogram/detail/fill_n.hpp:272:7: error: designator order for field 'boost::histogram::detail::fill_n(std::size_t, S&, A&, boost::histogram::detail::span<const T>, Us&& ...) [with S = boost::histogram::unlimited_storage<>; A = std::tuple<boost::histogram::axis::integer<int, boost::histogram::axis::null_type, boost::use_default> >; T = boost::variant2::variant<int, std::vector<int, std::allocator<int> > >; Us = {std::pair<const double*, long unsigned int>}; std::size_t = long unsigned int]::<lambda(const auto:93&)>::<us#0 capture>' does not match declaration order in 'boost::histogram::detail::fill_n(std::size_t, S&, A&, boost::histogram::detail::span<const T>, Us&& ...) [with S = boost::histogram::unlimited_storage<>; A = std::tuple<boost::histogram::axis::integer<int, boost::histogram::axis::null_type, boost::use_default> >; T = boost::variant2::variant<int, std::vector<int, std::allocator<int> > >; Us = {std::pair<const double*, long unsigned int>}; std::size_t = long unsigned int]::<lambda(const auto:93&)>'  
/Users/henryiii/git/software/histogram-python/extern/histogram/include/boost/histogram/detail/fill_n.hpp:285:80: error: redeclaration of 'std::pair<const double*, long unsigned int>& us#0'  
  285 |         fill_n_1(offset, storage, axes, vsize, values.data(), std::forward<Us>(us)...);  
      |                                                                                ^~  
/Users/henryiii/git/software/histogram-python/extern/histogram/include/boost/histogram/detail/fill_n.hpp:284:57: note: 'std::pair<const double*, long unsigned int>& us#0' previously declared here  
  284 |         fill_n_check_extra_args(vsize, std::forward<Us>(us)...);  
      |                                                         ^~  
/Users/henryiii/git/software/histogram-python/extern/histogram/include/boost/histogram/detail/fill_n.hpp:279:7: error: member 'boost::histogram::detail::fill_n(std::size_t, S&, A&, boost::histogram::detail::span<const T>, Us&& ...) [with S = boost::histogram::unlimited_storage<>; A = std::tuple<boost::histogram::axis::integer<int, boost::histogram::axis::null_type, boost::use_default> >; T = boost::variant2::variant<int, std::vector<int, std::allocator<int> > >; Us = {std::pair<const double*, long unsigned int>}; std::size_t = long unsigned int]::<lambda(const auto:94&)>::<us#0 capture>' is uninitialized reference  
  279 |       [&](const auto& values) {  
      |       ^~~~~~~~~~~~~~~~~~~~~~~~~  
  280 |         if (axes_rank(axes) != values.size())  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  281 |           BOOST_THROW_EXCEPTION(  
      |           ~~~~~~~~~~~~~~~~~~~~~~  
  282 |               std::invalid_argument("number of arguments must match histogram rank"));  
      |               ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  283 |         const auto vsize = get_total_size(values);  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  284 |         fill_n_check_extra_args(vsize, std::forward<Us>(us)...);  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  285 |         fill_n_1(offset, storage, axes, vsize, values.data(), std::forward<Us>(us)...);  
      |         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  286 |       },  
      |       ~  
/Users/henryiii/git/software/histogram-python/extern/histogram/include/boost/histogram/detail/fill_n.hpp:279:7: error: designator order for field 'boost::histogram::detail::fill_n(std::size_t, S&, A&, boost::histogram::detail::span<const T>, Us&& ...) [with S = boost::histogram::unlimited_storage<>; A = std::tuple<boost::histogram::axis::integer<int, boost::histogram::axis::null_type, boost::use_default> >; T = boost::variant2::variant<int, std::vector<int, std::allocator<int> > >; Us = {std::pair<const double*, long unsigned int>}; std::size_t = long unsigned int]::<lambda(const auto:94&)>::<us#0 capture>' does not match declaration order in 'boost::histogram::detail::fill_n(std::size_t, S&, A&, boost::histogram::detail::span<const T>, Us&& ...) [with S = boost::histogram::unlimited_storage<>; A = std::tuple<boost::histogram::axis::integer<int, boost::histogram::axis::null_type, boost::use_default> >; T = boost::variant2::variant<int, std::vector<int, std::allocator<int> > >; Us = {std::pair<const double*, long unsigned int>}; std::size_t = long unsigned int]::<lambda(const auto:94&)>'  
/Users/henryiii/git/software/histogram-python/extern/histogram/include/boost/histogram/detail/fill_n.hpp: In instantiation of 'boost::histogram::detail::fill_n(std::size_t, S&, A&, boost::histogram::detail::span<const T>, Us&& ...) [with S = boost::histogram::unlimited_storage<>; A = std::tuple<boost::histogram::axis::integer<int, boost::histogram::axis::null_type, boost::use_default> >; T = boost::variant2::variant<int, std::vector<int, std::allocator<int> > >; Us = {std::pair<const double*, long unsigned int>}; std::size_t = long unsigned int]::<lambda(const auto:94&)> [with auto:94 = boost::histogram::detail::span<const boost::variant2::variant<int, std::vector<int, std::allocator<int> > >, 18446744073709551615>]':  
/Users/henryiii/git/software/histogram-python/extern/histogram/include/boost/histogram/detail/static_if.hpp:26:56:   required from 'constexpr decltype(auto) boost::histogram::detail::static_if_impl(std::false_type, T&&, F&&, Args&& ...) [with T = boost::histogram::detail::fill_n(std::size_t, S&, A&, boost::histogram::detail::span<const T>, Us&& ...) [with S = boost::histogram::unlimited_storage<>; A = std::tuple<boost::histogram::axis::integer<int, boost::histogram::axis::null_type, boost::use_default> >; T = boost::variant2::variant<int, std::vector<int, std::allocator<int> > >; Us = {std::pair<const double*, long unsigned int>}; std::size_t = long unsigned int]::<lambda(const auto:93&)>; F = boost::histogram::detail::fill_n(std::size_t, S&, A&, boost::histogram::detail::span<const T>, Us&& ...) [with S = boost::histogram::unlimited_storage<>; A = std::tuple<boost::histogram::axis::integer<int, boost::histogram::axis::null_type, boost::use_default> >; T = boost::variant2::variant<int, std::vector<int, std::allocator<int> > >; Us = {std::pair<const double*, long unsigned int>}; std::size_t = long unsigned int]::<lambda(const auto:94&)>; Args = {const boost::histogram::detail::span<const boost::variant2::variant<int, std::vector<int, std::allocator<int> > >, 18446744073709551615>&}; std::false_type = std::integral_constant<bool, false>]'  
/Users/henryiii/git/software/histogram-python/extern/histogram/include/boost/histogram/detail/static_if.hpp:38:28:   required from 'constexpr decltype(auto) boost::histogram::detail::static_if(Ts&& ...) [with Bool = std::integral_constant<bool, false>; Ts = {boost::histogram::detail::fill_n(std::size_t, S&, A&, boost::histogram::detail::span<const T>, Us&& ...) [with S = boost::histogram::unlimited_storage<>; A = std::tuple<boost::histogram::axis::integer<int, boost::histogram::axis::null_type, boost::use_default> >; T = boost::variant2::variant<int, std::vector<int, std::allocator<int> > >; Us = {std::pair<const double*, long unsigned int>}; std::size_t = long unsigned int]::<lambda(const auto:93&)>, boost::histogram::detail::fill_n(std::size_t, S&, A&, boost::histogram::detail::span<const T>, Us&& ...) [with S = boost::histogram::unlimited_storage<>; A = std::tuple<boost::histogram::axis::integer<int, boost::histogram::axis::null_type, boost::use_default> >; T = boost::variant2::variant<int, std::vector<int, std::allocator<int> > >; Us = {std::pair<const double*, long unsigned int>}; std::size_t = long unsigned int]::<lambda(const auto:94&)>, const boost::histogram::detail::span<const boost::variant2::variant<int, std::vector<int, std::allocator<int> > >, 18446744073709551615>&}]'  
/Users/henryiii/git/software/histogram-python/extern/histogram/include/boost/histogram/detail/fill_n.hpp:271:80:   required from 'void boost::histogram::detail::fill_n(std::size_t, S&, A&, boost::histogram::detail::span<const T>, Us&& ...) [with S = boost::histogram::unlimited_storage<>; A = std::tuple<boost::histogram::axis::integer<int, boost::histogram::axis::null_type, boost::use_default> >; T = boost::variant2::variant<int, std::vector<int, std::allocator<int> > >; Us = {std::pair<const double*, long unsigned int>}; std::size_t = long unsigned int]'  
/Users/henryiii/git/software/histogram-python/extern/histogram/include/boost/histogram/histogram.hpp:232:19:   required from 'void boost::histogram::histogram<Axes, Storage>::fill(const Iterable&, const boost::histogram::weight_type<T>&) [with Iterable = std::vector<boost::variant2::variant<int, std::vector<int, std::allocator<int> > > >; T = std::vector<double, std::allocator<double> >&; <template-parameter-2-3> = boost::histogram::detail::requires_iterable<std::vector<boost::variant2::variant<int, std::vector<int, std::allocator<int> > > >, void>; Axes = std::tuple<boost::histogram::axis::integer<int, boost::histogram::axis::null_type, boost::use_default> >; Storage = boost::histogram::unlimited_storage<>]'  
/Users/henryiii/git/software/histogram-python/extern/histogram/test/histogram_fill_test.cpp:132:5:   required from 'void run_tests(const std::vector<int>&, const std::vector<int>&, const std::vector<double, std::allocator<double> >&) [with Tag = std::integral_constant<bool, false>]'  
/Users/henryiii/git/software/histogram-python/extern/histogram/test/histogram_fill_test.cpp:290:32:   required from here  
/Users/henryiii/git/software/histogram-python/extern/histogram/include/boost/histogram/detail/fill_n.hpp:285:80: error: redeclaration of 'std::pair<const double*, long unsigned int>& us#0'  
  285 |         fill_n_1(offset, storage, axes, vsize, values.data(), std::forward<Us>(us)...);  
      |                                                                                ^~  
/Users/henryiii/git/software/histogram-python/extern/histogram/include/boost/histogram/detail/fill_n.hpp:284:57: note: 'std::pair<const double*, long unsigned int>& us#0' previously declared here  
  284 |         fill_n_check_extra_args(vsize, std::forward<Us>(us)...);  
      |                                                         ^~  
/Users/henryiii/git/software/histogram-python/extern/histogram/include/boost/histogram/detail/fill_n.hpp: In instantiation of 'void boost::histogram::detail::fill_n(std::size_t, S&, A&, boost::histogram::detail::span<const T>, Us&& ...) [with S = boost::histogram::unlimited_storage<>; A = std::tuple<boost::histogram::axis::integer<int, boost::histogram::axis::null_type, boost::use_default>, boost::histogram::axis::integer<int, boost::histogram::axis::null_type, boost::histogram::axis::option::bitset<0> > >; T = boost::variant2::variant<int, std::vector<int, std::allocator<int> > >; Us = {std::pair<const int*, long unsigned int>}; std::size_t = long unsigned int]':  
  
...  
  
/Users/henryiii/git/software/histogram-python/extern/histogram/include/boost/histogram/histogram.hpp:276:22:   required from 'void boost::histogram::histogram<Axes, Storage>::fill(const Iterable&, const boost::histogram::weight_type<T>&, const boost::histogram::sample_type<U>&) [with Iterable = std::array<boost::variant2::variant<int, std::vector<int, std::allocator<int> > >, 2>; T = const std::vector<double, std::allocator<double> >&; U = std::tuple<const std::vector<double, std::allocator<double> >&>; <template-parameter-2-4> = boost::histogram::detail::requires_iterable<std::array<boost::variant2::variant<int, std::vector<int, std::allocator<int> > >, 2>, void>; Axes = std::vector<boost::histogram::axis::variant<boost::histogram::axis::integer<int, boost::histogram::axis::null_type, boost::use_default>, boost::histogram::axis::integer<int, boost::histogram::axis::null_type, boost::histogram::axis::option::bitset<0> >, boost::histogram::axis::regular<double, boost::use_default, boost::histogram::axis::null_type, boost::use_default>, boost::histogram::axis::variable<double, boost::histogram::axis::null_type, boost::use_default, std::allocator<double> >, boost::histogram::axis::category<int, boost::histogram::axis::null_type, boost::use_default, std::allocator<int> > >, std::allocator<boost::histogram::axis::variant<boost::histogram::axis::integer<int, boost::histogram::axis::null_type, boost::use_default>, boost::histogram::axis::integer<int, boost::histogram::axis::null_type, boost::histogram::axis::option::bitset<0> >, boost::histogram::axis::regular<double, boost::use_default, boost::histogram::axis::null_type, boost::use_default>, boost::histogram::axis::variable<double, boost::histogram::axis::null_type, boost::use_default, std::allocator<double> >, boost::histogram::axis::category<int, boost::histogram::axis::null_type, boost::use_default, std::allocator<int> > > > >; Storage = boost::histogram::storage_adaptor<std::vector<boost::histogram::accumulators::weighted_mean<>, std::allocator<boost::histogram::accumulators::weighted_mean<> > > >]'  
/Users/henryiii/git/software/histogram-python/extern/histogram/include/boost/histogram/histogram.hpp:300:5:   required from 'void boost::histogram::histogram<Axes, Storage>::fill(const Iterable&, const boost::histogram::sample_type<T>&, const boost::histogram::weight_type<U>&) [with Iterable = std::array<boost::variant2::variant<int, std::vector<int, std::allocator<int> > >, 2>; T = std::tuple<const std::vector<double, std::allocator<double> >&>; U = const std::vector<double, std::allocator<double> >&; <template-parameter-2-4> = boost::histogram::detail::requires_iterable<std::array<boost::variant2::variant<int, std::vector<int, std::allocator<int> > >, 2>, void>; Axes = std::vector<boost::histogram::axis::variant<boost::histogram::axis::integer<int, boost::histogram::axis::null_type, boost::use_default>, boost::histogram::axis::integer<int, boost::histogram::axis::null_type, boost::histogram::axis::option::bitset<0> >, boost::histogram::axis::regular<double, boost::use_default, boost::histogram::axis::null_type, boost::use_default>, boost::histogram::axis::variable<double, boost::histogram::axis::null_type, boost::use_default, std::allocator<double> >, boost::histogram::axis::category<int, boost::histogram::axis::null_type, boost::use_default, std::allocator<int> > >, std::allocator<boost::histogram::axis::variant<boost::histogram::axis::integer<int, boost::histogram::axis::null_type, boost::use_default>, boost::histogram::axis::integer<int, boost::histogram::axis::null_type, boost::histogram::axis::option::bitset<0> >, boost::histogram::axis::regular<double, boost::use_default, boost::histogram::axis::null_type, boost::use_default>, boost::histogram::axis::variable<double, boost::histogram::axis::null_type, boost::use_default, std::allocator<double> >, boost::histogram::axis::category<int, boost::histogram::axis::null_type, boost::use_default, std::allocator<int> > > > >; Storage = boost::histogram::storage_adaptor<std::vector<boost::histogram::accumulators::weighted_mean<>, std::allocator<boost::histogram::accumulators::weighted_mean<> > > >]'  
/Users/henryiii/git/software/histogram-python/extern/histogram/test/histogram_fill_test.cpp:258:5:   required from 'void run_tests(const std::vector<int>&, const std::vector<int>&, const std::vector<double, std::allocator<double> >&) [with Tag = std::integral_constant<bool, true>]'  
/Users/henryiii/git/software/histogram-python/extern/histogram/test/histogram_fill_test.cpp:291:33:   required from here  
/Users/henryiii/git/software/histogram-python/extern/histogram/include/boost/histogram/detail/fill_n.hpp:285:80: error: redeclaration of 'std::pair<const double*, long unsigned int>& us#0'  
  285 |         fill_n_1(offset, storage, axes, vsize, values.data(), std::forward<Us>(us)...);  
      |                                                                                ^~  
/Users/henryiii/git/software/histogram-python/extern/histogram/include/boost/histogram/detail/fill_n.hpp:284:57: note: 'std::pair<const double*, long unsigned int>& us#0' previously declared here  
  284 |         fill_n_check_extra_args(vsize, std::forward<Us>(us)...);  
      |                                                         ^~  
/Users/henryiii/git/software/histogram-python/extern/histogram/include/boost/histogram/detail/fill_n.hpp:285:80: error: redeclaration of 'std::pair<const double*, long unsigned int>& us#1'  
  285 |         fill_n_1(offset, storage, axes, vsize, values.data(), std::forward<Us>(us)...);  
      |                                                                                ^~  
/Users/henryiii/git/software/histogram-python/extern/histogram/include/boost/histogram/detail/fill_n.hpp:284:57: note: 'std::pair<const double*, long unsigned int>& us#1' previously declared here  
  284 |         fill_n_check_extra_args(vsize, std::forward<Us>(us)...);  
      |                                                         ^~  
make[4]: *** [test/CMakeFiles/BoostHistogram-histogram_fill_test_cpp.dir/histogram_fill_test.cpp.o] Error 1  
make[3]: *** [test/CMakeFiles/BoostHistogram-histogram_fill_test_cpp.dir/all] Error 2  
make[2]: *** [test/CMakeFiles/BoostHistogram-histogram_fill_test_cpp.dir/rule] Error 2  
make[1]: *** [BoostHistogram-histogram_fill_test_cpp] Error 2  
```  
  
Full version:  
[log.txt](https://github.com/boostorg/histogram/files/3710165/log.txt)

---

## Comment 1

> Username: henryiii  
> Created at: 2019-10-10 04:06:12 UTC  
> Url: https://github.com/boostorg/histogram/issues/234#issuecomment-540340130  

It works just fine in GCC 8, by the way. To reproduce, from a current checkout, run:  
  
```bash  
docker run --rm -v $PWD:/histogram -it gcc:9  
wget -qO- "https://cmake.org/files/v3.15/cmake-3.15.4-Linux-x86_64.tar.gz" | tar --strip-components=1 -xz -C /usr/local  
cmake -S histogram -B build  
cmake --build build  
cmake --build build -t test  
```  
  
(Changing 9 to 8 fixes the problem)

---

## Comment 2

> Username: HDembinski  
> Created at: 2019-10-10 07:39:55 UTC  
> Updated at: 2019-10-10 07:40:10 UTC  
> Url: https://github.com/boostorg/histogram/issues/234#issuecomment-540438710  

Thank you for reporting, will fix. Not sure whether it is a bug in my code or the compiler, but usually newer versions are more standard compliant than older versions, so maybe a subtle bug in the code. These things happen.

---

## Comment 3

> Username: HDembinski  
> Created at: 2019-10-10 09:07:19 UTC  
> Url: https://github.com/boostorg/histogram/issues/234#issuecomment-540475267  

It looks like a bug in gcc-9. I implemented a workaround.

---

## Comment 4

> Username: henryiii  
> Created at: 2019-10-10 12:23:34 UTC  
> Url: https://github.com/boostorg/histogram/issues/234#issuecomment-540546995  

I thought it looked like a compiler bug. Have you reported it to gcc our verified it already is known?

---

## Comment 5

> Username: HDembinski  
> Created at: 2019-10-11 20:31:33 UTC  
> Url: https://github.com/boostorg/histogram/issues/234#issuecomment-541213652  

No, I am too lazy to prepare a minimal example.
