# #47 - boost::variant + move only types + gcc 4.7.2 = regression since boost 1.55? [Closed]

> Username: Dushistov  
> Created at: 2018-03-06 19:10:17 UTC  
> Updated at: 2018-03-08 10:44:45 UTC  
> Closed at: 2018-03-07 00:58:28 UTC  
> Url: https://github.com/boostorg/variant/issues/47  

I try to compile such code with gcc 4.7.2 (g++ -std=c++11):  
  
```C++  
#include <boost/variant.hpp>  
#include <vector>  
#include <memory>  
  
  
int main()  
{   
  using VarType = boost::variant<std::unique_ptr<double>, int>;  
  std::vector<VarType> v;  
  v.emplace_back(5);  
}  
```  
  
and boost-1.66.0. This code doesn't compile, and give error:  
```  
/home/user/boost_1_66_0/boost/variant/variant.hpp:467:9: error: use of deleted function ‘std::unique_ptr<_Tp, _Dp>::unique_ptr(const std::unique_ptr<_Tp, _Dp>&) [with _Tp = double; _Dp = std::default_delete<double>; std::unique_ptr<_Tp, _Dp> = std::unique_ptr<double>]’  
```  
[build.txt](https://github.com/boostorg/variant/files/1786339/build.txt)  
  
I thought may be too old compiler (I have to use this version of gcc),  
but with boost-1.55 this example compiled just fine without any problems.  
  
Any hints what is going on?

---

## Comment 1

> Username: rfgplk  
> Created at: 2018-03-06 23:14:37 UTC  
> Updated at: 2018-03-06 23:15:36 UTC  
> Url: https://github.com/boostorg/variant/issues/47#issuecomment-370963580  

It appears as though boost::variant isn't moving the value into the underlying container, merely copying it. For some reason, the library falls back on using the copy constructor instead of the move constructor for std::unique_ptr, which is illegal (due to common pointer-sense) hence why the standard disables copying from lvalues. Due to the older library differences, the types very likely cannot be unambiguously converted to the destination variant's types.   
See line 467: ` new(storage_) T(operand);`  
Should be calling line 507: ` new(storage_) T(::boost::detail::variant::move(operand));`  
  
As for a fix, you should be able to do a dirty hack by using std::shared_ptr, although I imagine you don't want to do that.

---

## Comment 2

> Username: Dushistov  
> Created at: 2018-03-06 23:41:15 UTC  
> Updated at: 2018-03-06 23:41:45 UTC  
> Url: https://github.com/boostorg/variant/issues/47#issuecomment-370969438  

Thank you, @rfgplk for reply.  
  
> As for a fix, you should be able to do a dirty hack by using std::shared_ptr, although I imagine you don't want to do that.  
  
Yeah, this is the last variant. `std::unique_ptr` was an example of move only class. In real code I use my own move only type, so I have to do two allocations in case of `std::shared_ptr`.  
  
> Due to the older library differences, the types very likely cannot be unambiguously converted to the destination variant's types  
  
I start bisecting looks like the problem is in dependicies of `variant`, not in `variant` by it self.  
  
boost-1.60.0 - good  
boost-1.61.0 - bad  
  
but if I switch main repo to `boost-1.60.0` and `variant` submodule to `boost-1.66.0` all were compiled fine.  
  
Will try to bisect concrete commit that cause problem.

---

## Comment 3

> Username: Dushistov  
> Created at: 2018-03-07 00:22:08 UTC  
> Url: https://github.com/boostorg/variant/issues/47#issuecomment-370977651  

@rfgplk   
  
I found commit that cause problem:  
  
https://github.com/boostorg/type_traits/commit/1f3a3a1553dc316ed002354dce3ba3f081b2e102  
  
After this change from @jzmaddock `gcc 4.7.2`, `boost::variant`  and `std::unique_ptr`  ceased to be friends.

---

## Comment 4

> Username: rfgplk  
> Created at: 2018-03-07 00:47:39 UTC  
> Updated at: 2018-03-07 00:53:52 UTC  
> Url: https://github.com/boostorg/variant/issues/47#issuecomment-370982448  

Looks like you pinpointed the correct commit, nice work. The problem is that the boost source is literally full of workarounds / cheap fixes instead of providing proper solutions to problems.   
  
If you stated that the code worked properly on `boost-1.60.0`, you could add an additional workaround for your specific compiler version reversing the change.  
ie   
  
`#if BOOST_WORKAROUND(BOOST_GCC_VERSION, != YOUR_VERSION) && BOOST_WORKAROUND(BOOST_GCC_VERSION, < 40700)`  
or whatever you need to do.  
This way it should maintain full compatibility with the old code and allow you to use your features, but the decision on the boost team to support such `workarounds` is ridiculous since it permits unreadable and nonmaintainable code in the future.

---

## Comment 5

> Username: Dushistov  
> Created at: 2018-03-07 00:58:28 UTC  
> Url: https://github.com/boostorg/variant/issues/47#issuecomment-370984590  

Just for record (may be google bring somebody here), I solved it in such way:  
  
```diff  
--- a/boost/type_traits/is_nothrow_move_constructible.hpp  
+++ b/boost/type_traits/is_nothrow_move_constructible.hpp  
@@ -27,7 +27,7 @@ struct is_nothrow_move_constructible : public integral_constant<bool, BOOST_IS_N  
 template <class T> struct is_nothrow_move_constructible<volatile T> : public ::boost::false_type {};  
 template <class T> struct is_nothrow_move_constructible<const volatile T> : public ::boost::false_type{};  
   
-#elif !defined(BOOST_NO_CXX11_NOEXCEPT) && !defined(BOOST_NO_SFINAE_EXPR) && !BOOST_WORKAROUND(BOOST_GCC_VERSION, < 40800)  
+#elif !defined(BOOST_NO_CXX11_NOEXCEPT) && !defined(BOOST_NO_SFINAE_EXPR) && !BOOST_WORKAROUND(BOOST_GCC_VERSION, < 40700)  
   
 #include <boost/type_traits/declval.hpp>  
 #include <boost/utility/enable_if.hpp>  
  
```  
  
@rfgplk, I am not sure that my `gcc` is pure vanilla, it is old, debian based distribution,  
 have no idea what patches were applied. May be it will works with my gcc 4.7.2 and failed with other gcc 4.7.2

---

## Comment 6

> Username: jzmaddock  
> Created at: 2018-03-07 18:30:56 UTC  
> Url: https://github.com/boostorg/variant/issues/47#issuecomment-371236856  

OK, what you've done is basically revert the patches for this issue: https://svn.boost.org/trac10/ticket/11904.  The basic issue here is that these traits frequently fail to compile with gcc-4.7.x, which in turn breaks Boost.Optional.  But as you've discovered, the fix breaks variant in this specific situation.  
  
Longer explanation:  
  
* There will always be some older compiler which either don't implement noexcept or rvalue references for which these traits necessarily return false for all types.   The workaround is absolutely required in that situation and hopefully uncontroversial.  
* There are a number of compiler releases which do implement noexcept and/or rvalue refs, but they are too buggy to be used in these traits.  Normally this is harmless, albeit you loose some optimisations.  The one exception is a moveable, but non-copyable type as you've discovered.  
  
I'm looking at this again in case I made a mistake (not unheard of, but don't hold your breath).

---

## Comment 7

> Username: jzmaddock  
> Created at: 2018-03-07 18:35:00 UTC  
> Url: https://github.com/boostorg/variant/issues/47#issuecomment-371238225  

>Looks like you pinpointed the correct commit, nice work. The problem is that the boost source is literally full of workarounds / cheap fixes instead of providing proper solutions to problems.  
  
The compiler frequently generates internal-compiler-errors when you use noexcept, what exactly would be a proper fix?  
  
>but the decision on the boost team to support such workarounds is ridiculous since it permits unreadable and nonmaintainable code in the future.  
  
OK, we could ship just the standard conforming code and remove the workarounds, then all older gcc versions like this would just choke on the header which would hardly help the OP.

---

## Comment 8

> Username: jzmaddock  
> Created at: 2018-03-07 19:08:01 UTC  
> Url: https://github.com/boostorg/variant/issues/47#issuecomment-371249121  

Time for me to eat my words - the tests are all passing locally for me with gcc-4.7.4 so I've tentatively re-enabled support for is_nothrow_move_constructible in that compiler.  We'll see what happens with the online testers...

---

## Comment 9

> Username: Dushistov  
> Created at: 2018-03-07 19:41:37 UTC  
> Url: https://github.com/boostorg/variant/issues/47#issuecomment-371259683  

@jzmaddock I use gcc 4.7.2. If you have no this version of gcc,  
I can run tests using for your change it if you give hint how to run tests.

---

## Comment 10

> Username: jzmaddock  
> Created at: 2018-03-07 20:04:47 UTC  
> Url: https://github.com/boostorg/variant/issues/47#issuecomment-371266887  

from the boost root directory, and assuming you have already bootstrapped b2, then:  
  
cd libs/type_traits/test  
../../../b2 --toolset=gcc-4.7.2 cxxflags=-std=c++11  
  
But... I've just realised I inadvertently ran my tests in the default C++03 mode, so I need to go back and re-check.

---

## Comment 11

> Username: Dushistov  
> Created at: 2018-03-07 23:43:07 UTC  
> Url: https://github.com/boostorg/variant/issues/47#issuecomment-371325760  

@jzmaddock   
  
here is log of `./b2 -a  --toolset=gcc-4.7 cxxflags=-std=c++11 libs/type_traits/test 2>&1 | tee /tmp/type_traits.log`  
  
I run tests for https://github.com/boostorg/type_traits/commit/b06185c29f7eaca91c35da7671b076b6691afb2e  
  
[type_traits.log.txt](https://github.com/boostorg/variant/files/1791297/type_traits.log.txt)  
  
There is minor failure:  
```  
cc1plus: error: unrecognized command line option "-Wno-int-in-bool-context" [-Werror]  
cc1plus: error: unrecognized command line option "-Wno-bool-operation" [-Werror]  
cc1plus: all warnings being treated as errors  
```  
but this happens for `(failed-as-expected)` test as I understand,  
so one bug hide another bug?  
  
And one major failure - `is_list_constructible_test`, but I am not sure, is it related problem to our disscussion or not.

---

## Comment 12

> Username: Dushistov  
> Created at: 2018-03-08 00:16:17 UTC  
> Url: https://github.com/boostorg/variant/issues/47#issuecomment-371332406  

@jzmaddock I rerun tests without usage of `-Wno-int-in-bool-context` and `-Wno-bool-operation`:   
[type_traits2.log.txt](https://github.com/boostorg/variant/files/1791359/type_traits2.log.txt)  
  
still only `is_list_constructible_test` failed.

---

## Comment 13

> Username: jzmaddock  
> Created at: 2018-03-08 10:44:45 UTC  
> Url: https://github.com/boostorg/variant/issues/47#issuecomment-371451158  

Travis CI with 4.7.3 passed OK.
