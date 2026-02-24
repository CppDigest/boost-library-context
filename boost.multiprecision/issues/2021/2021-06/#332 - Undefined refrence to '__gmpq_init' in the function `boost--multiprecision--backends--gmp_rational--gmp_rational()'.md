# #332 - Undefined refrence to  '__gmpq_init' in  the function `boost::multiprecision::backends::gmp_rational::gmp_rational()': [Closed]

> Username: bloodgroup-cplusplus  
> Created at: 2021-06-02 20:49:46 UTC  
> Updated at: 2021-06-03 15:24:38 UTC  
> Closed at: 2021-06-03 15:24:37 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/332  

I got this issue while i was compiling a Convex Hull vectors using the CGAL 5.2.2 module the source code and the  stack trace is pasted below  
// this is the source code  
  
  
#include <CGAL/Exact_predicates_inexact_constructions_kernel.h>  
#include <CGAL/convex_hull_2.h>  
#include <vector>  
typedef CGAL::Exact_predicates_inexact_constructions_kernel K;  
typedef K::Point_2 Point_2;  
typedef std::vector<Point_2> Points;  
int main()  
{  
  Points points, result;  
  points.push_back(Point_2(0,0));  
  points.push_back(Point_2(10,0));  
  points.push_back(Point_2(10,10));  
  points.push_back(Point_2(6,5));  
  points.push_back(Point_2(4,1));  
  CGAL::convex_hull_2( points.begin(), points.end(), std::back_inserter(result) );  
  std::cout << result.size() << " points on the convex hull" << std::endl;  
  return 0;  
}  
  
The stack trace reads as follows:  
  
n file included from /usr/include/boost/smart_ptr/detail/sp_thread_sleep.hpp:22,  
                 from /usr/include/boost/smart_ptr/detail/yield_k.hpp:23,  
                 from /usr/include/boost/smart_ptr/detail/spinlock_gcc_atomic.hpp:14,  
                 from /usr/include/boost/smart_ptr/detail/spinlock.hpp:42,  
                 from /usr/include/boost/smart_ptr/detail/spinlock_pool.hpp:25,  
                 from /usr/include/boost/smart_ptr/shared_ptr.hpp:29,  
                 from /usr/include/boost/shared_ptr.hpp:17,  
                 from /usr/include/CGAL/Object.h:32,  
                 from /usr/include/CGAL/kernel_basic.h:25,  
                 from /usr/include/CGAL/basic.h:35,  
                 from /usr/include/CGAL/Cartesian/Cartesian_base.h:20,  
                 from /usr/include/CGAL/Simple_cartesian.h:20,  
                 from /usr/include/CGAL/Exact_predicates_inexact_constructions_kernel.h:20,  
                 from convex_hall.cpp:3:  
/usr/include/boost/bind.hpp:36:1: note: ‘#pragma message: The practice of declaring the Bind placeholders (_1, _2, ...) in the global namespace is deprecated. Please use <boost/bind/bind.hpp> + using namespace boost::placeholders, or define BOOST_BIND_GLOBAL_PLACEHOLDERS to retain the current behavior.’  
   36 | BOOST_PRAGMA_MESSAGE(  
      | ^~~~~~~~~~~~~~~~~~~~  
/usr/bin/ld: /tmp/ccqGkByx.o: in function `boost::multiprecision::backends::gmp_rational::gmp_rational()':  
convex_hall.cpp:(.text._ZN5boost14multiprecision8backends12gmp_rationalC2Ev[_ZN5boost14multiprecision8backends12gmp_rationalC5Ev]+0x18): undefined reference to `__gmpq_init'  
/usr/bin/ld: /tmp/ccqGkByx.o: in function `boost::multiprecision::backends::gmp_rational::gmp_rational(boost::multiprecision::backends::gmp_rational const&)':  
convex_hall.cpp:(.text._ZN5boost14multiprecision8backends12gmp_rationalC2ERKS2_[_ZN5boost14multiprecision8backends12gmp_rationalC5ERKS2_]+0x1c): undefined reference to `__gmpq_init'  
/usr/bin/ld: convex_hall.cpp:(.text._ZN5boost14multiprecision8backends12gmp_rationalC2ERKS2_[_ZN5boost14multiprecision8backends12gmp_rationalC5ERKS2_]+0x3c): undefined reference to `__gmpq_set'  
/usr/bin/ld: /tmp/ccqGkByx.o: in function `boost::multiprecision::backends::gmp_rational::operator=(double)':  
convex_hall.cpp:(.text._ZN5boost14multiprecision8backends12gmp_rationalaSEd[_ZN5boost14multiprecision8backends12gmp_rationalaSEd]+0x2a): undefined reference to `__gmpq_init'  
/usr/bin/ld: convex_hall.cpp:(.text._ZN5boost14multiprecision8backends12gmp_rationalaSEd[_ZN5boost14multiprecision8backends12gmp_rationalaSEd]+0x3f): undefined reference to `__gmpq_set_d'  
/usr/bin/ld: /tmp/ccqGkByx.o: in function `boost::multiprecision::backends::gmp_rational::~gmp_rational()':  
convex_hall.cpp:(.text._ZN5boost14multiprecision8backends12gmp_rationalD2Ev[_ZN5boost14multiprecision8backends12gmp_rationalD5Ev]+0x32): undefined reference to `__gmpq_clear'  
/usr/bin/ld: /tmp/ccqGkByx.o: in function `boost::multiprecision::backends::gmp_rational::compare(boost::multiprecision::backends::gmp_rational const&) const':  
convex_hall.cpp:(.text._ZNK5boost14multiprecision8backends12gmp_rational7compareERKS2_[_ZNK5boost14multiprecision8backends12gmp_rational7compareERKS2_]+0x5c): undefined reference to `__gmpq_cmp'  
/usr/bin/ld: /tmp/ccqGkByx.o: in function `CGAL::Mpzf::Mpzf(CGAL::Mpzf const&)':  
convex_hall.cpp:(.text._ZN4CGAL4MpzfC2ERKS0_[_ZN4CGAL4MpzfC5ERKS0_]+0x8d): undefined reference to `__gmpn_copyi'  
/usr/bin/ld: /tmp/ccqGkByx.o: in function `CGAL::Mpzf::aors(CGAL::Mpzf const&, CGAL::Mpzf const&, int)':  
convex_hall.cpp:(.text._ZN4CGAL4Mpzf4aorsERKS0_S2_i[_ZN4CGAL4Mpzf4aorsERKS0_S2_i]+0xe5): undefined reference to `__gmpn_copyi'  
/usr/bin/ld: convex_hall.cpp:(.text._ZN4CGAL4Mpzf4aorsERKS0_S2_i[_ZN4CGAL4Mpzf4aorsERKS0_S2_i]+0x185): undefined reference to `__gmpn_copyi'  
/usr/bin/ld: convex_hall.cpp:(.text._ZN4CGAL4Mpzf4aorsERKS0_S2_i[_ZN4CGAL4Mpzf4aorsERKS0_S2_i]+0x349): undefined reference to `__gmpn_copyi'  
/usr/bin/ld: convex_hall.cpp:(.text._ZN4CGAL4Mpzf4aorsERKS0_S2_i[_ZN4CGAL4Mpzf4aorsERKS0_S2_i]+0x39a): undefined reference to `__gmpn_copyi'  
/usr/bin/ld: /tmp/ccqGkByx.o:convex_hall.cpp:(.text._ZN4CGAL4Mpzf4aorsERKS0_S2_i[_ZN4CGAL4Mpzf4aorsERKS0_S2_i]+0x3f8): more undefined references to `__gmpn_copyi' follow  
/usr/bin/ld: /tmp/ccqGkByx.o: in function `CGAL::Mpzf::aors(CGAL::Mpzf const&, CGAL::Mpzf const&, int)':  
convex_hall.cpp:(.text._ZN4CGAL4Mpzf4aorsERKS0_S2_i[_ZN4CGAL4Mpzf4aorsERKS0_S2_i]+0x597): undefined reference to `__gmpn_add'  
/usr/bin/ld: convex_hall.cpp:(.text._ZN4CGAL4Mpzf4aorsERKS0_S2_i[_ZN4CGAL4Mpzf4aorsERKS0_S2_i]+0x625): undefined reference to `__gmpn_add'  
/usr/bin/ld: convex_hall.cpp:(.text._ZN4CGAL4Mpzf4aorsERKS0_S2_i[_ZN4CGAL4Mpzf4aorsERKS0_S2_i]+0x93c): undefined reference to `__gmpn_copyi'  
/usr/bin/ld: convex_hall.cpp:(.text._ZN4CGAL4Mpzf4aorsERKS0_S2_i[_ZN4CGAL4Mpzf4aorsERKS0_S2_i]+0x9b8): undefined reference to `__gmpn_neg'  
/usr/bin/ld: convex_hall.cpp:(.text._ZN4CGAL4Mpzf4aorsERKS0_S2_i[_ZN4CGAL4Mpzf4aorsERKS0_S2_i]+0xa0b): undefined reference to `__gmpn_sub_1'  
/usr/bin/ld: convex_hall.cpp:(.text._ZN4CGAL4Mpzf4aorsERKS0_S2_i[_ZN4CGAL4Mpzf4aorsERKS0_S2_i]+0xab9): undefined reference to `__gmpn_neg'  
/usr/bin/ld: convex_hall.cpp:(.text._ZN4CGAL4Mpzf4aorsERKS0_S2_i[_ZN4CGAL4Mpzf4aorsERKS0_S2_i]+0xb25): undefined reference to `__gmpn_sub'  
/usr/bin/ld: convex_hall.cpp:(.text._ZN4CGAL4Mpzf4aorsERKS0_S2_i[_ZN4CGAL4Mpzf4aorsERKS0_S2_i]+0xb50): undefined reference to `__gmpn_sub_1'  
/usr/bin/ld: /tmp/ccqGkByx.o: in function `CGAL::operator*(CGAL::Mpzf const&, CGAL::Mpzf const&)':  
convex_hall.cpp:(.text._ZN4CGALmlERKNS_4MpzfES2_[_ZN4CGALmlERKNS_4MpzfES2_]+0xea): undefined reference to `__gmpn_mul'  
/usr/bin/ld: convex_hall.cpp:(.text._ZN4CGALmlERKNS_4MpzfES2_[_ZN4CGALmlERKNS_4MpzfES2_]+0x13d): undefined reference to `__gmpn_mul'  
collect2: error: ld returned 1 exit status

---

## Comment 1

> Username: ofloveandhate  
> Created at: 2021-06-03 03:24:34 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/332#issuecomment-853532987  

This looks to me that you need to link `gmp` to your executable.

---

## Comment 2

> Username: ckormanyos  
> Created at: 2021-06-03 07:45:13 UTC  
> Updated at: 2021-06-03 07:47:25 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/332#issuecomment-853657949  

>> while i was compiling a Convex Hull vectors  
  
> you need to link `gmp`  
  
That seems likely to be needed. What build system or build commands are you using? On `*nix` when linking, simply include `-lgmp` and ensure that GMP is installed in a standard location.  
  
This is actually a bit of a subtle case that does not receive tremendous amounts of discussion. Although the GMP interface in Boost.Multiprecision is header-only C++, you still do, when using either the GMP or MPFR backends need to link with GMP for the GMP backend and both GMP as well as MPFR for the MPFR.

---

## Comment 3

> Username: bloodgroup-cplusplus  
> Created at: 2021-06-03 14:41:02 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/332#issuecomment-853922700  

yup it does work after linking with gmp.. thanks guys..

---

## Comment 4

> Username: ckormanyos  
> Created at: 2021-06-03 15:24:37 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/332#issuecomment-853955171  

> it does work after linking with gmp.  
  
Thanks for your confirmation. Closing issue.
