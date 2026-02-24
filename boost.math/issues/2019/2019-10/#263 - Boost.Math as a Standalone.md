# #263 - Boost.Math as a Standalone [Closed]

> Username: NAThompson  
> Created at: 2019-10-15 16:54:54 UTC  
> Updated at: 2019-10-17 14:05:52 UTC  
> Closed at: 2019-10-17 14:05:52 UTC  
> Url: https://github.com/boostorg/math/issues/263  

Just a discussion point: What would be the effort to get a boost.math standalone working? Obviously we'd need to bring in multiprecision, mp11, and config. Is there anything else?  
  
How much effort would it be?

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-10-15 17:24:21 UTC  
> Url: https://github.com/boostorg/math/issues/263#issuecomment-542319423  

Quite a bit I suspect.  Multiprecision isn't required.  Config, MPL, and lexical_cast certainly.  Maybe others, not sure.

---

## Comment 2

> Username: ax3l  
> Created at: 2019-10-15 20:24:55 UTC  
> Updated at: 2019-10-15 20:28:25 UTC  
> Url: https://github.com/boostorg/math/issues/263#issuecomment-542389035  

Just as a note in case it helps, [Boost.MP11](https://github.com/boostorg/mp11) supports usage as standalone CMake project which is a wonderful feature. Maybe we can copy the solution from there already, they depend on config, assert and core.  
  
Discussion: https://github.com/boostorg/mp11/pull/19  
Implementation: https://github.com/boostorg/mp11/blob/develop/CMakeLists.txt

---

## Comment 3

> Username: ax3l  
> Created at: 2019-10-15 21:12:23 UTC  
> Url: https://github.com/boostorg/math/issues/263#issuecomment-542407097  

Here is a `grep` of all includes in current `develop` for includes that are not `boost/math`:  
  
<details>  
<code>  
algorithm  
array  
atomic  
boost/algorithm/string/trim.hpp  
boost/archive/codecvt_null.hpp  
boost/archive/text_iarchive.hpp  
boost/archive/text_oarchive.hpp  
boost/archive/text_wiarchive.hpp  
boost/archive/text_woarchive.hpp  
boost/archive/xml_iarchive.hpp  
boost/archive/xml_oarchive.hpp  
boost/array.hpp  
boost/assert.hpp  
boost/atomic.hpp  
boost/bind.hpp  
boost/bind/placeholders.hpp  
boost/chrono.hpp  
boost/concept_archetype.hpp  
boost/concept_check.hpp  
boost/concepts/std_real_concept.hpp  
boost/config/header_deprecated.hpp  
boost/config.hpp  
boost/config/no_tr1/cmath.hpp  
boost/config/no_tr1/complex.hpp  
boost/core/demangle.hpp  
boost/core/enable_if.hpp  
boost/core/lightweight_test.hpp  
boost/cstdfloat.hpp  
boost/cstdint.hpp  
boost/cstdlib.hpp  
boost/current_function.hpp  
boost/detail/endian.hpp  
boost/detail/fenv.hpp  
boost/detail/lightweight_mutex.hpp  
boost/detail/workaround.hpp  
boost/exception/exception.hpp  
boost/filesystem/fstream.hpp  
boost/filesystem.hpp  
boost/fixed_point/fixed_point.hpp  
boost/format.hpp  
boost/function/function1.hpp  
boost/function.hpp  
boost/fusion/include/std_pair.hpp  
boost/fusion/include/tuple.hpp  
boost/integer/common_factor_ct.hpp  
boost/integer/common_factor_rt.hpp  
boost/interprocess/sync/named_mutex.hpp  
boost/interprocess/sync/scoped_lock.hpp  
boost/iostreams/stream.hpp  
boost/iostreams/tee.hpp  
boost/lambda/lambda.hpp  
boost/lexical_cast.hpp  
boost/limits.hpp  
boost/mp11/function.hpp  
boost/mp11/integer_sequence.hpp  
boost/mp11/integral.hpp  
boost/mp11/list.hpp  
boost/mp11/tuple.hpp  
boost/mp11/utility.hpp  
boost/mpl/and.hpp  
boost/mpl/at.hpp  
boost/mpl/bool.hpp  
boost/mpl/comparison.hpp  
boost/mpl/contains.hpp  
boost/mpl/divides.hpp  
boost/mpl/equal_to.hpp  
boost/mpl/find_if.hpp  
boost/mpl/greater_equal.hpp  
boost/mpl/greater.hpp  
boost/mpl/has_xxx.hpp  
boost/mpl/if.hpp  
boost/mpl/int.hpp  
boost/mpl/joint_view.hpp  
boost/mpl/less_equal.hpp  
boost/mpl/list.hpp  
boost/mpl/not.hpp  
boost/mpl/or.hpp  
boost/mpl/plus.hpp  
boost/mpl/push_back.hpp  
boost/mpl/remove_if.hpp  
boost/mpl/size.hpp  
boost/mpl/vector.hpp  
boost/multiprecision/complex128.hpp  
boost/multiprecision/cpp_bin_float.hpp  
boost/multiprecision/cpp_bin_float/io.hpp  
boost/multiprecision/cpp_complex.hpp  
boost/multiprecision/cpp_dec_float.hpp  
boost/multiprecision/cpp_int.hpp  
boost/multiprecision/debug_adaptor.hpp  
boost/multiprecision/float128.hpp  
boost/multiprecision/integer.hpp  
boost/multiprecision/mpc.hpp  
boost/multiprecision/mpfi.hpp  
boost/multiprecision/mpfr.hpp  
boost/numeric/ublas/io.hpp  
boost/numeric/ublas/lu.hpp  
boost/numeric/ublas/matrix.hpp  
boost/numeric/ublas/operation.hpp  
boost/numeric/ublas/vector.hpp  
boost/predef/architecture/x86.h  
boost/predef/other/endian.h  
boost/preprocessor/stringize.hpp  
boost/program_options.hpp  
boost/random.hpp  
boost/random/mersenne_twister.hpp  
boost/random/uniform_int.hpp  
boost/random/uniform_real_distribution.hpp  
boost/range/adaptors.hpp  
boost/range/begin.hpp  
boost/range/end.hpp  
boost/range.hpp  
boost/range/irange.hpp  
boost/range/size.hpp  
boost/regex.hpp  
boost/scoped_array.hpp  
boost/serialization/nvp.hpp  
boost/spirit/include/classic_actor.hpp  
boost/spirit/include/classic_core.hpp  
boost/static_assert.hpp  
boost/svg_plot/show_2d_settings.hpp  
boost/svg_plot/svg_2d_plot.hpp  
boost/system/error_code.hpp  
boost/test/floating_point_comparison.hpp  
boost/test/included/prg_exec_monitor.hpp  
boost/test/included/unit_test.hpp  
boost/test/results_collector.hpp  
boost/test/test_case_template.hpp  
boost/test/test_tools.hpp  
boost/test/tools/floating_point_comparison.hpp  
boost/test/unit_test.hpp  
boost/test/unit_test_log.hpp  
boost/throw_exception.hpp  
boost/timer/timer.hpp  
boost/tuple/tuple_comparison.hpp  
boost/tuple/tuple.hpp  
boost/type_index.hpp  
boost/typeof/typeof.hpp  
boost/type_traits/conditional.hpp  
boost/type_traits/has_pre_increment.hpp  
boost/type_traits.hpp  
boost/type_traits/integral_constant.hpp  
boost/type_traits/is_class.hpp  
boost/type_traits/is_complex.hpp  
boost/type_traits/is_constructible.hpp  
boost/type_traits/is_convertible.hpp  
boost/type_traits/is_detected.hpp  
boost/type_traits/is_floating_point.hpp  
boost/type_traits/is_fundamental.hpp  
boost/type_traits/is_integral.hpp  
boost/type_traits/is_pod.hpp  
boost/type_traits/is_same.hpp  
boost/type_traits/is_same.hpp  
boost/type_traits/remove_cv.hpp  
boost/utility/declval.hpp  
boost/utility/enable_if.hpp  
boost/utility/identity_type.hpp  
boost/version.hpp  
cctype  
cerrno  
cfloat  
chrono  
climits  
cmath  
complex  
cstddef  
cstring  
e_float/e_float.h  
emmintrin.h  
exception  
fftw3.h  
float.h  
fstream  
functional  
functions/functions.h  
future  
gmpfrxx.h  
initializer_list  
iomanip  
ios  
iosfwd  
iostream  
istream  
iterator  
limits  
limits.h  
locale  
map  
math.h  
memory  
mpreal.h  
mutex  
NTL/RR.h  
numeric  
ostream  
quadmath.h  
random  
set  
sstream  
stddef.h  
stdexcept  
stdlib.h  
string  
thread  
tuple  
typeinfo  
type_traits  
utility  
valarray  
vector  
</code>  
</details>  
  
So in summary:  
- `boost/algorithm/string/trim.hpp`  
- `boost/archive/`  
- `boost/bind/`  
- `boost/concepts/std_real_concept.hpp`  
- `boost/config/`  
- `boost/core/`  
- `boost/filesystem/`  
- `boost/function/`  
- `boost/fusion/`  
- `boost/integer/`  
- `boost/interprocess/`  
- `boost/iostreams/`  
- `boost/lambda/`  
- both `boost/mp11/` and `boost/mpl`  
- `boost/multiprecision/`  
- `boost/numeric/`  
- `boost/predef/` and `boost/preprocessor/`  
- `boost/program_options.hpp`  
- `boost/random/`  
- `boost/range/`  
- `boost/regex.hpp`  
- `boost/serialization/`  
- `boost/spirit/`  
- `boost/svg_plot/`  
- `boost/system/`  
- `boost/test/`  
- `boost/timer/`  
- `boost/tuple/`  
- `boost/typeof/`  
- `boost/type_traits/`  
- `boost/utility/`

---

## Comment 4

> Username: NAThompson  
> Created at: 2019-10-16 00:15:48 UTC  
> Url: https://github.com/boostorg/math/issues/263#issuecomment-542456097  

Looks pretty daunting to me. In other news, boost is really useful, so it uses itself extensively!  
  
@ax3l : If you want all why don't you just own your boost dependency and start putting all the generic numerical utilities you need for your PIC code into boost.math? They are probably things I've already thought about adding at some point . . .

---

## Comment 5

> Username: ax3l  
> Created at: 2019-10-16 02:58:19 UTC  
> Url: https://github.com/boostorg/math/issues/263#issuecomment-542488535  

We actually just want bessel functions, two integrators (de_tanh_sinh, gauss_kronrod) and a root finder (bracket_solve) to calculate a little lookup table beforehand ;-)  
  
No seriously, that's too heavy to embrace for the compilers we face daily (EDG frontends such as NVCC doing fancy code splitting, clang -x cuda, Cray, XL, Intel, PGI, ...). We were looking for something lightweight and small, both build-system wise as well as transitive dependencies.
