# #33 - Deducing quat/vec/mat type from deduce_scalar not longer sufficient [Closed]

> Username: federkamm  
> Created at: 2021-07-19 07:20:05 UTC  
> Updated at: 2021-07-28 18:24:18 UTC  
> Closed at: 2021-07-22 04:25:19 UTC  
> Url: https://github.com/boostorg/qvm/issues/33  

On the QVM version in Boost 1.71, one could overload `deduce_scalar` in order to use the QVM types with custom scalar types.  In current master, I have to overload `deduce_scalar_detail::deduce_scalar_impl` instead.  This is, because e.g. `deduce_vec2` in `deduce_vec.hpp` defaults the `S` template parameter to `deduce_scalar_detail::deduce_scalar_impl` instead of `deduce_scalar`.  Is this on purpose to prevent custom `deduce_scalar` rules for `deduce_vec2`, etc.?

---

## Comment 1

> Username: federkamm  
> Created at: 2021-07-19 11:30:48 UTC  
> Url: https://github.com/boostorg/qvm/issues/33#issuecomment-882474718  

This program compiles in Boost 1.71 (on Debian 10 with GCC 8.3.0)  
  
```c++  
#include <boost/qvm.hpp>  
using namespace boost::qvm;  
  
template <typename T> struct Wrap { T t; };  
template <typename S, typename T> Wrap<T> operator * (S s, Wrap<T> w) { return Wrap<T>{s * w.t}; }  
template <typename T> Wrap<T> operator + (Wrap<T> a, Wrap<T> b) { return Wrap<T>{a.t + b.t}; }  
  
namespace boost::qvm {  
template <typename S, typename T> struct deduce_scalar<S, Wrap<T>> { using type = Wrap<typename deduce_scalar<S, T>::type>; };  
//template <typename S, typename T, int R, int C> struct deduce_vec2<mat<S, R, C>, vec<Wrap<T>, C>, R> { using type = vec<Wrap<typename deduce_scalar<S, T>::type>, R>; };  
}  
  
int main() {  
  mat<double, 2, 2> m{};  
  vec<Wrap<double>, 2> v{};  
  m * v;  
  return 0;  
}  
```  
  
but not on current master.  It compiles on current master, when I uncomment the `deduce_vec2` line but then there are still some issues with other types than `vec<T, N>` like `vref` or similar.  
  
It also compiles with the `deduce_scalar` line replaced by  
  
```c++  
namespace deduce_scalar_detail {  
template <typename S, typename T> struct deduce_scalar_impl<S, Wrap<T>> { using type = Wrap<typename deduce_scalar<S, T>::type>; };  
}  
```  
  
but for this I have to mess in a `_detail` namespace which I probably shouldn't do.  
  
In my case, `Wrap<T>` is a `ceres::Jet<double, N>` and `S` simply a `double`.

---

## Comment 2

> Username: zajo  
> Created at: 2021-07-22 04:25:19 UTC  
> Url: https://github.com/boostorg/qvm/issues/33#issuecomment-884648053  

Please confirm that latest `develop` works for you.

---

## Comment 3

> Username: federkamm  
> Created at: 2021-07-22 08:22:34 UTC  
> Url: https://github.com/boostorg/qvm/issues/33#issuecomment-884737836  

Hi, thanks, it does work for me again.  
  
I have one other issue which I did consider to be a "bug" in Google Ceres, but I now found also a solution in QVM that might be worth to consider in general and would like to ask you about your oppinion about it.  
  
In Google Ceres, I can multiply a `Jet` by a `double` but for some reason not by an `int`.  So I added a function  
  
```c++  
template <typename T, int N>  
Jet<T,N> operator * (int x, Jet<T,N> j) { return T(x) * j; }  
```  
  
where `T` is always `double` in my case.  I need this, because the QVM operation `quat * vec` for rotating a vector by a quaternion fails otherwise in the line  
  
```c++  
vec_traits<R>::template write_element<0>(r) = 2*((t8+t10)*bx + (t6-t4)*by + (t3+t7)*bz) + bx;  
```  
  
because of the multiplication with the integer 2.  Changing this to 2.0 would solve my current problem but obviously cause problems in other situations.  However, when I change the line to  
  
```c++  
typedef typename vec_traits<R>::scalar_type T;  
vec_traits<R>::template write_element<0>(r) = scalar_traits<T>::value(2)*((t8+t10)*bx + (t6-t4)*by + (t3+t7)*bz) + bx;  
```  
  
and the following two lines accordantly, it solves the problem for me and looks sane to me in general.  
  
I do not really consider the current state wrong in QVM as I would expect from any sane scalar type to support arithmetic operations with `int`s, so in this case, I would consider Google Ceres broken.  However, this could add an extra layer of robustness to QVM to work around 3rd-party library bugs.  
  
Do you think it would be in general a good idea to wrap all plain `int`s in the calculations provided by QVM into the appropriate `scalar_traits<T>::value` functions?

---

## Comment 4

> Username: zajo  
> Created at: 2021-07-22 23:27:31 UTC  
> Url: https://github.com/boostorg/qvm/issues/33#issuecomment-885300827  

When you have:  
```  
T x = a * b;  
```  
This is not equivalent to:  
```  
T x = T(a) * b;  
```  
What if the expression on the right needs high precision or greater range to compute the value, but the value itself fits nicely into a `T`? I'm openminded about suggestions to fix the issue you're reporting but I'm not sure the solution you're proposing is appropriate.

---

## Comment 5

> Username: federkamm  
> Created at: 2021-07-28 12:08:28 UTC  
> Url: https://github.com/boostorg/qvm/issues/33#issuecomment-888256485  

I tried to think a little bit about it and I agree, it's kind of complex.  From the code of `quat * vec` and `deduce_scalar` it looks a little bit like so far you mainly considered elementary scalar types (like `char`, `int`, or `float`) but no compound ones like `std::complex` or user defined types for e.g. hypercomplex numbers (where the dual numbers belong to that Google Ceres Jet<T,N> type implements).  For the elementary types, there are a lot of implicit conversion rules to consider where as with the user-defined types one has to expect to probably deal with some expression-objects instead of values for some operations and has to think about which one to finalize to a value and which to keep around for possible optimizations.  
  
I think, it would be best, to collect some cases, in which one or another way works better/worse/not at all and then decide what to do on a sufficiently large scope of cases.  
  
Btw. QVM has a very good mechanism to allow compatibility with other libraries.  However, there are no compatibility layers to any other library available (as far as I see).  E.g. to work with Google Ceres I use this "glue" code:  
  
```c++  
using namespace ceres;  
using namespace boost::qvm;  
namespace boost::qvm {  
template <typename T, int N>  
struct is_scalar<Jet<T, N>> { static constexpr bool value = true; };  
template <typename S, typename T, int N>  
struct deduce_scalar<S, Jet<T, N>> { using type = Jet<typename deduce_scalar<S, T>::type, N>; };  
template <typename S, typename T, int N>  
struct deduce_scalar<Jet<S, N>, T> { using type = Jet<typename deduce_scalar<S, T>::type, N>; };  
template <typename S, typename T, int N>  
struct deduce_scalar<Jet<S, N>, Jet<T, N>> { using type = Jet<typename deduce_scalar<S, T>::type, N>; };  
}  
template <typename T, int N>  
Jet<T, N> operator * (int x, Jet<T, N> j) { return T(x) * j; }  
```  
  
where the last two lines work around the issue that `double * Jet<double, N>` works but `int * Jet<double, N>` doesn't.  Is there a place where I can find (or put) code like this, when I want to use QVM with other libraries (e.g. Eigen)?  
  
Another thing, for Google Ceres I used e.g. `qref` and `ptr_qref` but I couldn't find `ptr_qref` in the official documentation and I had to include `quat_traits_array.hpp` manually to do so.  Is the header not included by `all.hpp` on purpose and does `ptr_qref` not belong to the public interface of QVM?  Also, I noted I had to often use something like `vref(ptr_vref<3>(p))` and would have prefered to simply write `ptr_vref<3>(p)` instead.  Is this on pupose that `ptr_vref` does just return a (plain) array and not already a view proxy?

---

## Comment 6

> Username: zajo  
> Created at: 2021-07-28 18:24:18 UTC  
> Url: https://github.com/boostorg/qvm/issues/33#issuecomment-888524411  

I'll accept this integration in a pull request. Maybe put it in boost/qvm/interop/google_ceres.hpp? I have similar glue code for other libs, I can probably add that too.  
  
Why don't you create an issue for the pointer thing. And in general, please open issues for such... issues. :)
