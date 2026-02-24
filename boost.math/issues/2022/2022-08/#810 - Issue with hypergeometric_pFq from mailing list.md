# #810 - Issue with hypergeometric_pFq from mailing list [Closed]

> Username: mborland  
> Created at: 2022-08-04 01:57:39 UTC  
> Updated at: 2022-08-04 15:42:03 UTC  
> Closed at: 2022-08-04 15:31:12 UTC  
> Url: https://github.com/boostorg/math/issues/810  

Hi,  
I'm trying to use Boost in order to find values of a 2F3 hypergeometric function. Just to check that I am using the package the correct way, I tried to compile the small test file below (using the example from the pFq docs), with the command  
  
```  
$ g++ -o test.exe test.cpp -I /home/peder/boost/boost_1_79_0  
```  
  
As far as I understand, the library is header-only, so I didn't expect the compilation error below. I don't where I've made a mistake, and I'd greatly appreciate any help with figuring out the error.   
  
I'm running Ubuntu 20.04.04 on WSL and using gcc 9.4.0.  
  
Best regards,  
Peder  
  
Test program:  
  
```  
#include <iostream>  
#include <boost/math/special_functions/hypergeometric_pFq.hpp>  
  
int main() {  
  
    double z  = -9;  
  
    double h = boost::math::hypergeometric_pFq({2,3,4}, {5,6,7,8}, z);  // Calculate 3F4  
  
    std::cout << h << std::endl;  
    return 0;  
}   
```  
  
Error message:  
```  
$ g++ -o test.exe test.cpp -I /home/peder/boost/boost_1_79_0  
/usr/bin/ld: /tmp/ccp4Lm8m.o: in function `std::pair<long double, long double> boost::math::detail::hypergeometric_pFq_checked_series_impl<std::initializer_list<int>, long double, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy>, boost::math::detail::iteration_terminator>(std::initializer_list<int> const&, std::initializer_list<int> const&, long double const&, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> const&, boost::math::detail::iteration_terminator const&, long long&)':  
test.cpp:(.text._ZN5boost4math6detail38hypergeometric_pFq_checked_series_implISt16initializer_listIiEeNS0_8policies6policyINS5_14default_policyES7_S7_S7_S7_S7_S7_S7_S7_S7_S7_S7_S7_EENS1_20iteration_terminatorEEESt4pairIT0_SB_ERKT_SF_RKSB_RKT1_RKT2_Rx[_ZN5boost4math6detail38hypergeometric_pFq_checked_series_implISt16initializer_listIiEeNS0_8policies6policyINS5_14default_policyES7_S7_S7_S7_S7_S7_S7_S7_S7_S7_S7_S7_EENS1_20iteration_terminatorEEESt4pairIT0_SB_ERKT_SF_RKSB_RKT1_RKT2_Rx]+0xeef): undefined reference to `boost::math::tools::promote_args<long double, float, float, float, float, float>::type boost::math::lgamma<long double, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >(long double, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> const&)'  
/usr/bin/ld: test.cpp:(.text._ZN5boost4math6detail38hypergeometric_pFq_checked_series_implISt16initializer_listIiEeNS0_8policies6policyINS5_14default_policyES7_S7_S7_S7_S7_S7_S7_S7_S7_S7_S7_S7_EENS1_20iteration_terminatorEEESt4pairIT0_SB_ERKT_SF_RKSB_RKT1_RKT2_Rx[_ZN5boost4math6detail38hypergeometric_pFq_checked_series_implISt16initializer_listIiEeNS0_8policies6policyINS5_14default_policyES7_S7_S7_S7_S7_S7_S7_S7_S7_S7_S7_S7_EENS1_20iteration_terminatorEEESt4pairIT0_SB_ERKT_SF_RKSB_RKT1_RKT2_Rx]+0xfe5): undefined reference to `boost::math::tools::promote_args<long double, float, float, float, float, float>::type boost::math::expm1<long double, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >(long double, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> const&)'  
/usr/bin/ld: /tmp/ccp4Lm8m.o: in function `int boost::math::detail::log_pochhammer<int, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >(int, unsigned int, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy>, int*)':  
test.cpp:(.text._ZN5boost4math6detail14log_pochhammerIiNS0_8policies6policyINS3_14default_policyES5_S5_S5_S5_S5_S5_S5_S5_S5_S5_S5_S5_EEEET_S7_jT0_Pi[_ZN5boost4math6detail14log_pochhammerIiNS0_8policies6policyINS3_14default_policyES5_S5_S5_S5_S5_S5_S5_S5_S5_S5_S5_S5_EEEET_S7_jT0_Pi]+0x3d): undefined reference to `boost::math::tools::promote_args<int, float, float, float, float, float>::type boost::math::lgamma<int, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >(int, int*, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> const&)'  
/usr/bin/ld: test.cpp:(.text._ZN5boost4math6detail14log_pochhammerIiNS0_8policies6policyINS3_14default_policyES5_S5_S5_S5_S5_S5_S5_S5_S5_S5_S5_S5_EEEET_S7_jT0_Pi[_ZN5boost4math6detail14log_pochhammerIiNS0_8policies6policyINS3_14default_policyES5_S5_S5_S5_S5_S5_S5_S5_S5_S5_S5_S5_EEEET_S7_jT0_Pi]+0x57): undefined reference to `boost::math::tools::promote_args<int, float, float, float, float, float>::type boost::math::lgamma<int, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >(int, int*, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> const&)'  
collect2: error: ld returned 1 exit status  
```

---

## Comment 1

> Username: mborland  
> Created at: 2022-08-04 15:34:38 UTC  
> Url: https://github.com/boostorg/math/issues/810#issuecomment-1205419511  

@pederlh This issue is fixed on develop now.

---

## Comment 2

> Username: pederlh  
> Created at: 2022-08-04 15:42:03 UTC  
> Url: https://github.com/boostorg/math/issues/810#issuecomment-1205429034  

Nice, thanks again for fixing the method!
