# #4 Enforce rounding in rounded_arith_opp to fix incorrect behaviour. [Open]

> Username: nafur  
> Created at: 2014-06-24 15:12:09 UTC  
> Updated at: 2025-10-18 08:23:05 UTC  
> Url: https://github.com/boostorg/interval/pull/4  

A discussion between Florian (corzilius@informatik.rwth-aachen.de) and Guillaume (guillaume.melquiond@inria.fr) lead to the proposed patch. Compiling the code stated below with `clang++ -std=c++11 -O1` leads to incorrect behaviour. (arch linux x64, clang 3.4.2, boost 1.55.0)  
In a nutshell, we compare the results for `41 * 0.1` and `-(-41 * 0.1)` and the resulting intervals turn out to be disjoint, none of them containing `4.1`. Note that tiny changes to the code (for example changing the order of the assignments) can make the error vanish.  
  
Guillaume proposed to either switch to `rounded_transc_std` or add `this->force_rounding` to all occurrences of `(-x)` after a deeper look into the generated assembly. (Thanks for this!) So here is a patch for this change.  
  
``` C++  
#include <iostream>  
#include <iomanip>  
#include <boost/numeric/interval.hpp>  
  
using namespace boost::numeric;  
using namespace boost::numeric::interval_lib;  
  
typedef save_state<rounded_transc_opp<double>> rnd;  
typedef checking_no_nan<double, checking_no_nan<double>> chk;  
typedef interval<double, policies<rnd, chk>> Interval;  
  
std::ostream& operator<<(std::ostream& os, const Interval& i) {  
    return os << std::setprecision(1000) << "[" << i.lower() << ", " << i.upper() << "]";  
}  
  
int main() {  
    Interval I1( double(41) );  
    Interval I2( -double(41) );  
    Interval M = Interval( double(1) ) / Interval( double(10) );  
    Interval R1 = I1 * M;  
    Interval R2 = -(I2 * M);  
  
    if (R1.lower() > R2.upper() || R1.lower() > R2.upper()) {  
        std::cout << "Error: the intervals" << std::endl;  
        std::cout << "\t   41 * 0.1  = " << R1 << std::endl;  
        std::cout << "\t-(-41 * 0.1) = " << R2 << std::endl;  
        std::cout << "\tare disjoint!" << std::endl;  
    }  
    return 0;  
}  
```

---

## Comment 1

> Username: mcquay239  
> Created_at: 2016-08-22 11:00:32 UTC  
> Url: https://github.com/boostorg/interval/pull/4#issuecomment-241379966  

Hello, @mclow, please, merge this PR. It's still relevant because of MSVC (still reproduced on MSVC 2015) over-optimization, see https://svn.boost.org/trac/boost/ticket/12018. Thank you!

---

## Comment 2

> Username: jeking3  
> Created_at: 2018-05-17 17:43:55 UTC  
> Url: https://github.com/boostorg/interval/pull/4#issuecomment-389950661  

I am adding a CI environment so we can better handle these requests and prove quality of changes.

---

## Comment 3

> Username: jeking3  
> Created_at: 2018-10-30 14:10:30 UTC  
> Url: https://github.com/boostorg/interval/pull/4#issuecomment-434316017  

This breaks test_add:  
```  
testing.capture-output ../../../bin.v2/libs/numeric/interval/test/add.test/gcc-7.3/debug/cxxstd-03-iso/visibility-hidden/add.run  
====== BEGIN OUTPUT ======  
/boost/libs/numeric/interval/test/add.cpp(223): test (test_add<I2>()) failed in function: 'int test_main(int, char**)'  
/boost/libs/numeric/interval/test/add.cpp(225): test (test_add1<I2>()) failed in function: 'int test_main(int, char**)'  
/boost/libs/numeric/interval/test/add.cpp(227): test (test_add2<I2>()) failed in function: 'int test_main(int, char**)'  
/boost/libs/numeric/interval/test/add.cpp(235): test (test_addeq<I2>()) failed in function: 'int test_main(int, char**)'  
/boost/libs/numeric/interval/test/add.cpp(237): test (test_addeq1<I2>()) failed in function: 'int test_main(int, char**)'  
  
**** 5 errors detected  
  
EXIT STATUS: 201  
====== END OUTPUT ======  
```

---

## Review 4 [Changes requested]

> Username: jeking3  
> Created_at: 2018-12-26 16:49:07 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/interval/pull/4#pullrequestreview-187926645  

Please rebase on develop.

---

## Comment 5

> Username: mglisse  
> Created_at: 2025-10-18 08:23:04 UTC  
> Url: https://github.com/boostorg/interval/pull/4#issuecomment-3417985174  

This feels like a hack. Either the compiler has a working `-frounding-math` (like recent versions of clang) and I don't see why this would be needed, or the compiler does not have a working `-frounding-math` (like gcc unless you add a `noinline` attribute in many places) and it is not sufficient, the compiler can still move arithmetic operations before the call to `fesetround` if motivated. To be safe, you would need to protect (write+read on a volatile variable for example) not just the result (already done) but also the arguments (1 of them should suffice, but protecting them all is obviously safer) of all operations.

---
