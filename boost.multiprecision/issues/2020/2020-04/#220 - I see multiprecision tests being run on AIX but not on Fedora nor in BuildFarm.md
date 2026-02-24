# #220 - I see multiprecision tests being run on AIX but not on Fedora nor in BuildFarm [Open]

> Username: trex58  
> Created at: 2020-04-15 08:08:41 UTC  
> Updated at: 2021-02-26 15:33:09 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/220  

Hello,  
I'm now porting Boost v1.69 to AIX.  
While running tests of Boost.Multiprecision to AIX, I see failing tests that I do not see being run on a Fedora/Intel machine (Boost v1.69) nor in the Boost buildfarm (https://www.boost.org/development/tests/master/developer/multiprecision.html).  
(They seem to deal with MPFR.)  
Moreover, about all Boost 1.69 multiprecision tests, I see 259 tests on the buildfarm, 324 on my Fedora machine, but about 530 on AIX.  
Why?  
Thx  
  
 ../boost/multiprecision/mpfr.hpp:166:134: error: no matching function for call to 'max(long unsigned int, mpfr_prec_t&)'  
  
 ../bin.v2/libs/multiprecision/test/test_arithmetic_mpc.test/gcc-9.3.0/debug/address-model-64/visibility-hidden/**test_arithmetic_mpc**.o...  
 ../bin.v2/libs/multiprecision/test/test_arithmetic_mpfr.test/gcc-9.3.0/debug/address-model-64/visibility-hidden/**test_arithmetic_mpfr**.o...  
 ../bin.v2/libs/multiprecision/test/test_arithmetic_mpfr_50.test/gcc-9.3.0/debug/address-model-64/visibility-hidden/**test_arithmetic_mpfr_50**.o...  
 ../bin.v2/libs/multiprecision/test/test_arithmetic_mpfr_50_static.test/gcc-9.3.0/debug/address-model-64/visibility-hidden/**test_arithmetic_mpfr_50_static**.o...  
 ../bin.v2/libs/multiprecision/test/test_mpfr_mpc_precisions.test/gcc-9.3.0/debug/address-model-64/visibility-hidden/**test_mpfr_mpc_precisions**.o...  
   
 ../bin.v2/libs/multiprecision/test/test_complex.test/gcc-9.3.0/debug/address-model-64/visibility-hidden/test_complex.o...  
 ../bin.v2/libs/multiprecision/test/test_numeric_limits_mpfr.test/gcc-9.3.0/debug/address-model-64/visibility-hidden/test_numeric_limits.o...  
 ../bin.v2/libs/multiprecision/test/test_numeric_limits_mpfr_50.test/gcc-9.3.0/debug/address-model-64/visibility-hidden/test_numeric_limits.o...  
 ../bin.v2/libs/multiprecision/test/test_move_mpfr.test/gcc-9.3.0/debug/address-model-64/visibility-hidden/test_move.o...  
 ../bin.v2/libs/multiprecision/test/test_exp_mpfr50.test/gcc-9.3.0/debug/address-model-64/visibility-hidden/test_exp.o...  
 ../bin.v2/libs/multiprecision/test/test_log_mpfr50.test/gcc-9.3.0/debug/address-model-64/visibility-hidden/test_log.o...  
 ../bin.v2/libs/multiprecision/test/test_pow_mpfr50.test/gcc-9.3.0/debug/address-model-64/visibility-hidden/test_pow.o...  
 ../bin.v2/libs/multiprecision/test/test_sqrt_mpfr50.test/gcc-9.3.0/debug/address-model-64/visibility-hidden/test_sqrt.o...

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-04-15 11:05:58 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/220#issuecomment-613972621  

Which tests are run depends on which third party libraries are installed, this looks like a bug at our end, and  
  
```  
      mpfr_init2(t, (std::max)(static_cast<unsigned long>(std::numeric_limits<boost::ulong_long_type>::digits), mpfr_get_prec(m_data)));  
```  
  
should be   
  
```  
      mpfr_init2(t, (std::max)(static_cast<mpfr_prec_t>(std::numeric_limits<boost::ulong_long_type>::digits), mpfr_get_prec(m_data)));  
```  
  
Although I confess I'm not sure if mpfr_prec_t was present through all recent mpfr releases?  
  
There may well be other places where this crops up as several other backends (mpc, mpfi) are more or less cut-and-pasted from the mpfr one.  
  
BTW on our CI tests we make sure that all possible dependencies are installed, but these are sadly rather Intel-centric.  The complete list should you want it is:  
  
gmp  
mpfr  
mpfi  
mpc  
tommath  
eigen  
  
If you can test the above at your end and either report back or submit a PR that would be great.  
  
Many thanks, keep the issues coming...

---

## Comment 2

> Username: trex58  
> Created at: 2020-04-23 06:10:11 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/220#issuecomment-618199532  

About the list of dependencies that are installed on my AIX and Fedora/Intel machines, it is the same:  **gmp, mpfr, libmpc** .  
Versions:   
 - AIX:          gmp v6.1.1 , mpfr v3.1.4 , libmpc v1.0.3  
 - Fed/Intel: gmp v6.1.2 , mpfr v3.1.6 , libmpc v1.1.0  
  
Quite the same. However, on Fed/Intel, there are plenty of multiprecision tests missing.  
So, the dependencies does not seem to be the root cause of so many multiprecision tests missing on Fedora/Intel. For Boost **v1.69** but also for **v1.72** . 9850 tests passed or failed for v1.69 and 11,000 tests passed or failed for v1.72 . About 327 tests only for multiprecision.  
  
About the 211 multiprecision tests missing on Fed/Intel vs AIX, there is:  **test_arithmetic_mpfr** , which, like many others, fails due to the error:  
../boost/multiprecision/mpfr.hpp:166:134: error: no matching function for call to 'max(long unsigned int, mpfr_prec_t&)'  
Maybe your comment about mpfr_init2() may solve this issue. I'll experiment asap.

---

## Comment 3

> Username: trex58  
> Created at: 2020-04-23 07:42:01 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/220#issuecomment-618237091  

The change suggested here above about "unsigned long" replaced by "mpfr_prec_t" made my compilation error for the test_arithmetic_mpfr  test to disappear. I'm now re-testing all multiprecision.  
Patch attached.  
[boost-1.69.0-mpfr-max.patch.txt](https://github.com/boostorg/multiprecision/files/4520941/boost-1.69.0-mpfr-max.patch.txt)  
  
The issue also appears in source code of Boost v1.72, and probably in v1.73 too .

---

## Comment 4

> Username: jzmaddock  
> Created at: 2020-04-23 07:45:20 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/220#issuecomment-618238629  

I think you're going to have to be specific about which tests are present on one platform but not the other.... and no I'm not sure how you go about figuring that out :(

---

## Comment 5

> Username: trex58  
> Created at: 2020-04-23 08:51:49 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/220#issuecomment-618271432  

Here are the 135 tests run today on AIX and not run on my Fed/Intel machines: AIX.txt .  
  
Here are the   32 tests run on my Fed/Intel machines and not run on AIX: FedIntel.txt .  
22 of these tests are not yet run on AIX because we do not build Boost with FP 128bit yet.  
  
[AIX.txt](https://github.com/boostorg/multiprecision/files/4521291/AIX.txt)  
[FedIntel.txt](https://github.com/boostorg/multiprecision/files/4521293/FedIntel.txt)  
  
Previously, on AIX, I had 539 multiprecision tests:   passed:326 - failed:107 - skipped:106 .  
Today, with the change above, I have 433 multiprecision tests:  passed:429 - failed:2 - skipped:2 .  
I do not understand yet why I have 103 less multiprecision tests on AIX today than before.

---

## Comment 6

> Username: trex58  
> Created at: 2020-04-23 09:24:27 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/220#issuecomment-618289092  

After some investigations about the discrepancy between my previous figures on AIX and today figures, I made a mistake. I see only 23 tests that were run previously on AIX and are no more run today still on AIX. 452 before and 429 today.  
  
These missing tests on AIX today vs my previous try are: test_acos test_arithmetic_mpc test_arithmetic_mpf.run test_asin test_atan test_constants test_cos test_cosh test_exp test_float_io test_fpclassify test_log test_move test_numeric_limits test_pow test_rat_float_interconv test_round test_sf_import_c99 test_sin test_sinh test_sqrt test_tan test_tanh . Don't know why...  
  
Anyway, there are 135 tests that are run on AIX and not on Fedora/Intel (my runs and on your Buildfarm summary).

---

## Comment 7

> Username: jzmaddock  
> Created at: 2020-04-23 11:12:57 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/220#issuecomment-618341177  

>The change suggested here above about "unsigned long" replaced by "mpfr_prec_t" made my compilation error for the test_arithmetic_mpfr test to disappear.   
  
Thanks I'll work up a PR later.

---

## Comment 8

> Username: jzmaddock  
> Created at: 2020-04-23 16:15:13 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/220#issuecomment-618493222  

The list in AIX.txt are all mpfr/gmp tests, when invoke b2, you should see at the start:  
  
    - has_mpfr builds          : yes (cached)  
  
and so on, my guess is that these are saying ":no" and that gmp and mpfr are not detected.  
  
If you've only recently installed them, and the results are cached, then invoking b2 with `--reconfigure` may do the trick.  If you think they should be found but still are not, then either look inside boost-root/bin.v2/config.log or invoke the test directly by:  
  
```  
cd libs/multiprecision/config  
../../../b2 toolset=whatever has_gmp has_mpfr  
```  
  
Similarly, the tests named in fedintel.txt are for the most part all tests of GCC's `__float128` type which requires being build with `-std=gnu++XX`.  Though I confess there are one or two listed there that don't appear to come from Boost.Multiprecision?  In any case in b2 parlance that means passing `cxxstd=XX cxxstd-dialect=gnu` on the command line if you want to enable those (where XX is one of 03, 11, 14, 17, 2a).  And of course for anything other than GCC they won't be run anyway.  
  
HTH, John.

---

## Comment 9

> Username: trex58  
> Created at: 2020-04-23 17:04:15 UTC  
> Updated at: 2020-04-23 17:23:00 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/220#issuecomment-618520388  

On **AIX**, I see:  
 `- has_mpfr builds          : yes`  
though on my two Fedora31/Intel machines, installed with same dependencies installed as on AIX, I see  
 `- has_mpfr_class builds    : no`  
 `- has_mpfr builds          : no`  
  
Now, on my **Fedora/Intel** machine:  
  - I see no word dealing with mpfr in `bin.v2/config.log` .  
  - mpfr was installed:  
         `# rpm -qi mpfr  :     Install Date: Thu 05 Dec 2019 09:08:11 AM EST`  
    though my first try to build Boost was later:  
         `Dec  5 09:50 boost-1.69.0-9.fc31.spec.res1`  
  
However, looking at more details, it seems to me that Boost not only requires mpfr to be installed, but also **mpfr-devel** (which requires gmp-devel).  
Thus, after I installed mpfr-devel and after I did:  
  ```  
cd libs/multiprecision/config  
  ../../../b2 has_gmp has_mpfr  :   
     ../../../bin.v2/libs/multiprecision/config/gcc-9.3.1/debug/visibility-hidden/has_mpfr_exe > ../../../bin.v2/libs/multiprecision/config/gcc-9.3.1/debug/visibility-hidden/**has_mpfr.output**  
```  
I now see :    
   `has_mpfr builds          : yes`  
in the output of the testing of multiprecision, now running.  
  
So, looking at Fedora boost.spec file (that I named boost-1.69.0-9.fc31.spec) I see that it does **NOT** contain any `Requires:` or `BuildRequires:` instruction about mpfr nor mpfr-devel . So, probably that mpfr is a dependency of a dependency of Boost and is automaticaly installed, but not mpfr-devel.  
Moreover, please note that the Fedora .spec file for building Boost does not run the tests:  
   ```  
%check  
   :   
```  
  
Thx !  
  
So, why the **buildfarm** does not show these MPFR tests ?  
Probably because mpfr-devel is not installed. Can you check ?  
  
Tony  
PS: multiprecision on Fedora31/x86_64 : **449 tests passed and 1 failed** (test_arithmetic_mpf : Segmentation fault. Also broken on AIX but for another reason). 429 passed on AIX out of 433 run.

---

## Comment 10

> Username: jzmaddock  
> Created at: 2020-04-25 16:09:59 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/220#issuecomment-619402298  

Sorry, I should have been clearer that it's the development packages that are required if you want to run those tests.  
  
Note that it's a strictly optional requirement - from the users POV they can use MPFR and or GMP or not as they wish.  It's only if you're determined to test everything that those are required ;)  
  
I'm also not sure why the spec file would contain a reference to mpfr - there's nothing in boost that requires a dependency on just the binaries - when we interact with 3rd party libraries it's an optional "interoperability" that requires the library's headers.  
  
>So, why the buildfarm does not show these MPFR tests ?  
>Probably because mpfr-devel is not installed. Can you check ?  
  
Do you mean here: https://www.boost.org/development/tests/develop/developer/multiprecision.html ?  
  
Those machines are donated by volunteers, I have no control over which 3rd party libraries are installed.  The CI tests on travis I do however, and those test all of the optional dependencies including MPFR.  
  
>multiprecision on Fedora31/x86_64 : 449 tests passed and 1 failed (test_arithmetic_mpf : Segmentation fault.  
  
I'd like to get to the bottom of that if you have any insights (stack backtrace?)  If possible it would help enormously if you could test the 1.73 release candidate as there have been a lot of changes since 1.69.  Note that fixes for Boost go into the next release - unless something truly grievous slips through there are no patches to previous releases.  The RC is here, and short of major SNAFU's will be the next official release within a week or so: https://dl.bintray.com/boostorg/release/1.73.0/source/

---

## Comment 11

> Username: jzmaddock  
> Created at: 2020-04-25 16:20:31 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/220#issuecomment-619404103  

The original std::max issue is addressed here: https://github.com/boostorg/multiprecision/pull/226.

---

## Comment 12

> Username: trex58  
> Created at: 2020-04-27 09:03:53 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/220#issuecomment-619840102  

> Sorry, I should have been clearer that it's the development packages that are required if you want to run those tests.  
  
Yes. However, each XYZ-dev package requires package XYZ, like for mpfr:  
%package devel  
Requires: %{name} = %{version}-%{release}  
  
> Note that it's a strictly optional requirement - from the users POV they can use MPFR and or GMP or not as they wish. It's only if you're determined to test everything that those are required ;)  
  
Yes. However, since there will be less users of Boost and Boost.multiprecision on AIX vs Fedora/Intel, we have to run all tests in order to guarantee best quality at first.  
  
> I'm also not sure why the spec file would contain a reference to mpfr - there's nothing in boost that requires a dependency on just the binaries - when we interact with 3rd party libraries it's an optional "interoperability" that requires the library's headers.  
  
Yes. I do not master Boost.  
I've only added in our .spec file:  
BuildRequires: mpfr-devel, gmp-devel  
  
> > So, why the buildfarm does not show these MPFR tests ?  
> > Probably because mpfr-devel is not installed. Can you check ?  
>   
> Do you mean here: https://www.boost.org/development/tests/develop/developer/multiprecision.html ?  
  
Yes. Looking at this page today, I see that test_arithmetic_mpf tests, and more, does now appear.  
:-)  
  
> > multiprecision on Fedora31/x86_64 : 449 tests passed and 1 failed (test_arithmetic_mpf : Segmentation fault.  
>   
> I'd like to get to the bottom of that if you have any insights (stack backtrace?) If possible it would help enormously if you could test the 1.73 release candidate as there have been a lot of changes since 1.69. Note that fixes for Boost go into the next release - unless something truly grievous slips through there are no patches to previous releases. The RC is here, and short of major SNAFU's will be the next official release within a week or so: https://dl.bintray.com/boostorg/release/1.73.0/source/  
  
What I see with v1.69.0 is:  
```  
....  
Testing mixed arithmetic with type: N5boost14multiprecision6numberINS0_8backends9gmp_floatILj0EEELNS0_26expression_template_optionE1EEE and N5boost14multiprecision6numberINS0_8backends7gmp_intELNS0_26expression_template_optionE1EEE  
  
Breakpoint 4, boost::multiprecision::number<boost::multiprecision::backends::gmp_float<0u>, (boost::multiprecision::expression_template_option)1>::number<boost::multiprecision::detail::add_immediates, boost::multiprecision::number<boost::multiprecision::backends::gmp_float<0u>, (boost::multiprecision::expression_template_option)1>, boost::multiprecision::number<boost::multiprecision::backends::gmp_int, (boost::multiprecision::expression_template_option)1>, void, void> (this=0x7fffffffa220, e=...)  
   at ../boost/multiprecision/number.hpp:333  
333   number t(e);  
(gdb) p e  
$10 = (const boost::multiprecision::detail::expression<boost::multiprecision::detail::add_immediates, boost::multiprecision::number<boost::multiprecision::backends::gmp_float<0>, (boost::multiprecision::expression_template_option)1>, boost::multiprecision::number<boost::multiprecision::backends::gmp_int, (boost::multiprecision::expression_template_option)1>, void, void> &) @0x7fffffffb550: {static left_depth = 1, static right_depth = 1, static depth = 1,  
 arg1 = @0x7fffffffa2f0, arg2 = @0x7fffffffb560}  
(gdb) p *this  
$11 = {m_backend = {<boost::multiprecision::backends::detail::gmp_float_imp<0>> =  
        {m_data = Template:Mp prec = 53, mp size = 0, mp exp = 0, mp d = 0x733640}, <No data fields>}}  
(gdb) p t  
$12 = {m_backend = {<boost::multiprecision::backends::detail::gmp_float_imp<0>> =  
        {m_data = [[:Template:Mp prec = 8, mp size = 0,  mp exp = 140737353439566, mp d = 0x1]]}, <No data fields>}}  
```  
  
It looks to me that we go into this code when e and *this do not have the same precision, and that **t** has been initialized with an enormous **exponent 140737353439566** before using e to build t.  Then I see objects like:  
```  
 (gdb) p this->m_data  
$1 = [[:Template:Mp prec = 33554423, mp size = 0, mp exp = 0, mp d = 0x0]]   
```  
with an enormous **precision 33554423** .  
Further execution is calling **number** constructor **87175 times** till memory is exhausted.  
I do not master C++ nor this code. But it seems to me that some initialization is not complete, non initializing correctly the exp field of t.  
It looks to me that this part of code is reached only within this kind of test with 2 Boost multiprecision objects. However, I may be wrong.  
  
I have tested with 1.70.0 first, and the failure is gone.  
Code of ../boost/multiprecision/number.hpp" has changed from:  
```  
  212     template <class tag, class Arg1, class Arg2, class Arg3, class Arg4>  
  213     typename boost::enable_if<is_convertible<typename detail::expression<tag, Arg1, Arg2, Arg3, Arg4>::result_type, self_type>, number&>::type operator=(const detail::expression<tag, Arg1, Arg2, Arg3, Arg4>& e)  
  214     {  
  215        typedef typename is_same<number, typename detail::expression<tag, Arg1, Arg2, Arg3, Arg4>::result_type>::type tag_type;  
  216        detail::scoped_default_precision<number<Backend, ExpressionTemplates> > precision_guard(e);  
  ...  
   226       BOOST_MP_CONSTEXPR_IF_VARIABLE_PRECISION(number)  
   227          if (boost::multiprecision::detail::current_precision_of(e) != boost::multiprecision::detail::current_precision_of(*this))  
   228          {  
   229             number t(e);  
   230             return *this = BOOST_MP_MOVE(t);  
   231          }  
   232       do_assign(e, tag_type());  
   233       return *this;  
   
   317    template <class tag, class Arg1, class Arg2, class Arg3, class Arg4>  
   318    number(const detail::expression<tag, Arg1, Arg2, Arg3, Arg4>& e, typename boost::enable_if_c<is_convertible<typename detail::expression<tag, Arg1 , Arg2, Arg3, Arg4>::result_type, self_type>::value>::type* = 0)  
   319    {  
   320       detail::scoped_default_precision<number<Backend, ExpressionTemplates> > precision_guard(e);  
   321       //  
   322       // If the current precision of *this differs from that of expression e, then we  
   323       // create a temporary (which will have the correct precision thanks to precision_guard)  
   324       // and then move the result into *this.  In C++17 we add a leading "if constexpr"  
   325       // which causes this code to be eliminated in the common case that this type is  
   326       // not actually variable precision.  Pre C++17 this code should still be mostly  
   327       // optimised away, but we can't prevent instantiation of the dead code leading  
   328       // to longer build and possibly link times.  
   329       //  
   330       BOOST_MP_CONSTEXPR_IF_VARIABLE_PRECISION(number)  
   331          if (boost::multiprecision::detail::current_precision_of(e) != boost::multiprecision::detail::current_precision_of(*this))  
   332          {  
   333             number t(e);  
   334             *this = BOOST_MP_MOVE(t);  
   335          }  
   336       *this = e;  
   337    }  
```  
to:  
```  
  
   212    template <class tag, class Arg1, class Arg2, class Arg3, class Arg4>  
   213    typename boost::enable_if<is_convertible<typename detail::expression<tag, Arg1, Arg2, Arg3, Arg4>::result_type, self_type>, number&>::type operator=(const detail::expression<tag, Arg1, Arg2, Arg3, Arg4>& e)  
   214    {  
   215       typedef typename is_same<number, typename detail::expression<tag, Arg1, Arg2, Arg3, Arg4>::result_type>::type tag_type;  
   216       detail::scoped_default_precision<number<Backend, ExpressionTemplates> > precision_guard(e);  
  ...  
   226       BOOST_MP_CONSTEXPR_IF_VARIABLE_PRECISION(number)  
   227       if (precision_guard.precision() != boost::multiprecision::detail::current_precision_of(*this))  
   228       {  
   229          number t(e);  
   230          return *this = BOOST_MP_MOVE(t);  
   231       }  
   232       do_assign(e, tag_type());  
   233       return *this;  
   234    }  
  ...  
   317    template <class tag, class Arg1, class Arg2, class Arg3, class Arg4>  
   318    number(const detail::expression<tag, Arg1, Arg2, Arg3, Arg4>& e, typename boost::enable_if_c<is_convertible<typename detail::expression<tag, Arg1 , Arg2, Arg3, Arg4>::result_type, self_type>::value>::type* = 0)  
   319    {  
   320       //  
   321       // No preicsion guard here, we already have one in operator=  
   322       //  
   323       *this = e;  
   324    }  
```  
However, this code is never reached in 1.70.0 .  
So, maybe the bug is still there but is not reached by tests?  
That's far too complicated for me...

---

## Comment 13

> Username: trex58  
> Created at: 2020-04-27 10:40:57 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/220#issuecomment-619896025  

> If possible it would help enormously if you could test the 1.73 release candidate as there have been a lot of changes since 1.69. Note that fixes for Boost go into the next release - unless something truly grievous slips through there are no patches to previous releases. The RC is here, and short of major SNAFU's will be the next official release within a week or so: https://dl.bintray.com/boostorg/release/1.73.0/source/  
  
I have started to build 1.73.0 on my Fedora/Intel machine. Next, I'll try on AIX - however it should take some time.

---

## Comment 14

> Username: trex58  
> Created at: 2020-04-27 13:40:10 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/220#issuecomment-619993066  

About Boost on Fedora, please note that they still deliver v1.69 due to failures to build 1.70 and more recent versions.  
See: https://fedoraproject.org/wiki/Changes/F32Boost172 and https://bugzilla.redhat.com/show_bug.cgi?id=1558278 .  
  
So, I cannot have a look at their more recent Boost .spec about how they fixed some issues common with AIX. Thus, I'm still using a .spec file designed for v1.69 for building Boost v1.73 . That may generate some differences though.  
  
Anyway, tests of v1.73.0 are now running in my Fedora/Intel environment.  
  
I also have built and tested v1.70.0 in same environment and I see 387 failing tests out of about 10,600 tests, with 346 gcc.link failures. Test process/async_pipe hangs.  
  
See attached file for more details:  
[v1.70.0Results.txt](https://github.com/boostorg/multiprecision/files/4539967/v1.70.0Results.txt)

---

## Comment 15

> Username: trex58  
> Created at: 2020-04-27 17:00:19 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/220#issuecomment-620110599  

Some results of run of Boost v1.73.0 tests on Fedora31/x86_64  -O0 :  
 - 10916 tests passed  
 -    383 tests failed, including 351 failing in gcc.link  
 -    312 tests skipped  
 - all tests multiprecision:test_arithmetic_mpf* are passed  
 - test process:async_pipe still hangs

---

## Comment 16

> Username: jzmaddock  
> Created at: 2020-04-27 18:16:47 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/220#issuecomment-620149654  

>About Boost on Fedora, please note that they still deliver v1.69 due to failures to build 1.70 and more recent versions.  
  
I've posted on the mailing list about this.  
  
>I also have built and tested v1.70.0 in same environment and I see 387 failing tests out of about 10,600 tests, with 346 gcc.link failures.  
  
Somewhat off topic for this issue as I don't see Multiprecision failures ;)  
  
However, the link errors do look like issues at our end - if you continue to see then in 1.73 can you please report to the respective libraries?  
  
For example:  
  
```  
    /usr/bin/ld: ../bin.v2/libs/timer/build/gcc-9.3.1/debug/threading-multi/visibility-hidden/libboost_timer.so.1.70.0:  
                 undefined reference to `boost::chrono::steady_clock::now()'  
```  
  
I suspect timer is unconditionally using chrono::steady_clock even though it's only conditionally available (depending on POSIX symbol CLOCK_MONOTONIC).  
  
```  
    /usr/bin/ld: ../bin.v2/libs/locale/build/gcc-9.3.1/debug/threadapi-pthread/threading-multi/visibility-hidden/libboost_locale.so.1.70.0:  
                 undefined reference to `boost::detail::set_tss_data(void const*, void (*)(void (*)(void*), void*), void (*)(void*), void*, bool)'  
```  
  
While TSS is only conditionally available, I think it always should be on POSIX systems so far as I can see.  Likewise:  
  
```  
    /usr/bin/ld: ../bin.v2/libs/coroutine/build/gcc-9.3.1/debug/threadapi-pthread/threading-multi/visibility-hidden/libboost_coroutine.so.1.70.0:  
                 undefined reference to `boost::thread_detail::enter_once_region(boost::once_flag&)'  
    collect2: error: ld returned 1 exit status  
    /usr/bin/ld: ../bin.v2/libs/coroutine/build/gcc-9.3.1/debug/threadapi-pthread/threading-multi/visibility-hidden/libboost_coroutine.so.1.70.0:  
                 undefined reference to `boost::thread_detail::rollback_once_region(boost::once_flag&)'  
```  
  
Look strange to me, it would help to see the command line that failed, and if you could check to see if those symbols are present in libboost_thread.  
  
```  
    /usr/bin/ld: ../bin.v2/libs/log/build/gcc-9.3.1/debug/threadapi-pthread/threading-multi/visibility-hidden/libboost_log_setup.so.1.70.0:  
                 undefined reference to `boost::re_detail_107000::perl_matcher<wchar_t const*, std::allocator<boost::sub_match<wchar_t const*> >, boost::regex_traits<wchar_t, boost::cpp_regex_traits<wchar_t> > >::construct_init(boost::basic_regex<wchar_t, boost::regex_traits<wchar_t, boost::cpp_regex_traits<wchar_t> > > const&, boost::regex_constants::_match_flags)'  
```  
  
As regex maintainer, that one is on me - that symbol should be explicitly instantiated in libs/regex/src/wregex.cpp and be present in libboost_regex, can you check?  The line of code that actually performs the instantiation is libs/regex/include/boost/regex/v4/instances.hpp:197

---

## Comment 17

> Username: trex58  
> Created at: 2020-04-28 12:40:14 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/220#issuecomment-620580566  

I have built/tested Boost v1.73.0 RC1 (which is the same as official v1.73.0) on Fedora31/x86_64 .  
  
First, remember that I am using Fedora .spec file of Boost v1.69.0 . They may have made mistakes, I may have made mistakes when patching their patches, the BuildRequires: and Requires: used by their .spec file may be uncomplete, I have installed RPMs by means of "yum install ...", the GCC compiler I'm using (provided by Fedora) may not be exactly the same as the one used by all machines in your BuildFarm, and - since Fedora does not run Boost tests, I've launched the tests by hand - following the process describred on Boost pages. I mean to say that my environment may be wrong in some places.  
However, I'm still surprised to see so many errors in such a reference environment.  
```  
passed:       10916  
failed:         383    (compile: 15 - link: 351 - testing: 7 - capture-output: 9)  
skipped:        312  
  
                       passed  failed  skipped  
       math              593      2        0  
       geometry          330    109      109  
       multiprecision    461      0        0  
```  
Amongst the failing tests, there are 351 at **link** time. Same kind as I noticed for Boost 1.70.0 .  
That could be due to some mistakes I did. But which mistakes?  
  
Moreover, there are also 16 failing tests at **gcc.compile.c++** time. I do not understand how that is possible since I'm using a recent GCC compiler (g++ 9.3.1 20200408) and I do not see how my environment (or my mistakes) could lead to such errors at compile time. However, I do not (and I will never) master Boost.  
Looking at BuildFarm for some of this failures at compile time, I do not see:  
  numeric/ublas: test_tensor_matrix_vector, test_algorithms, test_operators_arithmetic, test_expression_evaluation, and maybe more.  
  
However, I see on BuildFarm page some errors I have, like: numeric/odeint: runge_kutta_error_concepts .  
  
[Boost1.73.0-Fedora31-x86_64-issues.txt](https://github.com/boostorg/multiprecision/files/4545591/Boost1.73.0-Fedora31-x86_64-issues.txt)  
  
About the failures at test time, I see that some are common to BuildFarm, like: math/example: nonfinite_serialization_archives  
  
[Boost1.73.0-Fedora31-x86_64-failed-capture.txt](https://github.com/boostorg/multiprecision/files/4545663/Boost1.73.0-Fedora31-x86_64-failed-capture.txt)  
  
(Testing v1.73.0 on AIX is still running. However, that's my own business to understand/fix failures when they are due to the AIX & GCC environment, and there are still issues...)  
  
About your remaining above questions, I'll do my best to answer, but later today.

---

## Comment 18

> Username: jzmaddock  
> Created at: 2020-04-28 18:57:11 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/220#issuecomment-620793782  

I think I may just have to install Fedora and try locally, which Fedora version are you using?

---

## Comment 19

> Username: trex58  
> Created at: 2020-04-29 06:49:18 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/220#issuecomment-621021256  

> I think I may just have to install Fedora and try locally, which Fedora version are you using?  
```  
# cat /etc/fedora-release  
Fedora release 31 (Thirty One)  
  
# uname -a  
Linux destiny10 5.5.11-200.fc31.x86_64 #1 SMP Mon Mar 23 17:32:43 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux  
  
# cat /proc/cpuinfo | grep processor | wc  
     16   
  
vendor_id       : GenuineIntel  
cpu family      : 6  
model           : 26  
model name      : Intel(R) Xeon(R) CPU           X5550  @ 2.67GHz  
stepping        : 5  
microcode       : 0x1d  
cpu MHz         : 1599.751  
cache size      : 8192 KB  
flags           : fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush dts acpi mmx fxsr sse sse2 ht tm pbe syscall nx rdtscp lm constant_tsc arch_perfmon pebs bts rep_good nopl xtopology nonstop_tsc cpuid aperfmperf pni dtes64 monitor ds_cpl vmx est tm2 ssse3 cx16 xtpr pdcm dca sse4_1 sse4_2 popcnt lahf_lm pti ssbd ibrs ibpb stibp tpr_shadow vnmi flexpriority ept vpid dtherm flush_l1d  
bugs            : cpu_meltdown spectre_v1 spectre_v2 spec_store_bypass l1tf mds swapgs itlb_multihit  
  
# cat /proc/meminfo  
MemTotal:       16401808 kB  
SwapTotal:       8269820 kB  
  
# rpm -qi gcc  
Name        : gcc  
Version     : 9.3.1  
Release     : 2.fc31  
Architecture: x86_64  
Install Date: Mon 20 Apr 2020 06:39:39 AM EDT  
Source RPM  : gcc-9.3.1-2.fc31.src.rpm  
```  
For building/testing Boost 1.73.0, I'm using the .spec file attached to this message, which is derived from the Fedora 1.69.0 Fedora .spec file, with 3 patches removed and one changed to the one attached to this message. I was using the Boost 1.73.0 RC1 .bz2 file, which turned to be exactly the GA 1.73.0 .  
  
[boost-1.73.0-rc1.TONY.spec.txt](https://github.com/boostorg/multiprecision/files/4550093/boost-1.73.0-rc1.TONY.spec.txt)  
[boost-1.66.0-build-optflags-For1.70.patch.txt](https://github.com/boostorg/multiprecision/files/4550104/boost-1.66.0-build-optflags-For1.70.patch.txt)  
  
However, I've then found (but not used yet) this:  
http://denisarnaud.fedorapeople.org/boost/boost1.72/boost-1.72.0-1.fc32.src.rpm

---

## Comment 20

> Username: jwakely  
> Created at: 2020-04-29 20:31:41 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/220#issuecomment-621446887  

> So, I cannot have a look at their more recent Boost .spec  
  
See https://github.com/fedorapackaging/fedorareviews/tree/master/reviews/boost/boost_xxx_boost173

---

## Comment 21

> Username: trex58  
> Created at: 2020-04-30 10:09:25 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/220#issuecomment-621740168  

It looks to me that no one (Boost BuildFarm, Fedora project) has built/tested Boost 1.73.0 with GCC 9.3 .  
  
I have an issue with "unordered" library: test compile_map, which fails at compile time, both with AIX GCC 9.3 and Fedora31/x86_64 GCC 9.3.1 , but **not** with AIX GCC 8.4, and **not** on the Boost buildfarm, where older versions of GCC are used or CLANG is used.  
  
https://www.boost.org/development/tests/master/developer/unordered.html  
https://www.boost.org/development/tests/develop/developer/unordered.html  
  
 **AIX GCC 9.3 : KO !**  
```  
    "g++"  -fvisibility-inlines-hidden -fPIC -maix64 -pthread -mcmodel=large -O0 -fno-inline -Wall -fvisibility=hidden -O2 -ffp-contract=off  
    -pedantic -Wstrict-aliasing -fstrict-aliasing -Wextra -Wsign-promo -Wunused-parameter -Wconversion -Wfloat-equal -Wshadow -DBOOST_ALL_NO_LIB=1  
    -DBOOST_BEAST_ALLOW_DEPRECATED -DBOOST_BEAST_SEPARATE_COMPILATION -DBOOST_UNORDERED_USE_ALLOCATOR_TRAITS=0  -I".." -c  
    -o "../bin.v2/libs/unordered/test/compile_map_unordered_allocator.test/gcc-9/debug/address-model-64/threading-multi/visibility-hidden/unordered/compile_map.o"  
    "../libs/unordered/test/unordered/compile_map.cpp"  
   
 /opt/freeware/lib/gcc/powerpc-ibm-aix7.1.0.0/9/include/c++/ext/new_allocator.h:145:20:  
    error: use of deleted function 'constexpr std::tuple<_Elements>::tuple(const std::tuple<_Elements>&) [with _Elements = {int&&}]'  
  145 |  noexcept(noexcept(::new((void *)__p)  
      |                    ^  
  146 |        _Up(std::forward<_Args>(__args)...)))  
      |        ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^  
   
 ../bin.v2/libs/unordered/test/compile_map_unordered_allocator.test/gcc-9/debug/address-model-64/threading-multi/visibility-hidden/unordered/compile_map.o...  
  
  +141  #if __cplusplus >= 201103L  
  +142        template<typename _Up, typename... _Args>  
  +143          void  
  +144          construct(_Up* __p, _Args&&... __args)  
  +145          noexcept(noexcept(::new((void *)__p)  
  +146                              _Up(std::forward<_Args>(__args)...)))  
  +147          { ::new((void *)__p) _Up(std::forward<_Args>(__args)...); }  
```  
 **AIX GCC 8.4 : OK**  
```  
  "g++"  -fvisibility-inlines-hidden -fPIC -maix64 -pthread -mcmodel=large -O0 -fno-inline -Wall -fvisibility=hidden -O2 -ffp-contract=off -ftls-model=initial-exec  
  -pedantic -Wstrict-aliasing -fstrict-aliasing -Wextra -Wsign-promo -Wunused-parameter -Wconversion -Wfloat-equal -Wshadow -DBOOST_ALL_NO_LIB=1  
  -DBOOST_BEAST_ALLOW_DEPRECATED -DBOOST_BEAST_SEPARATE_COMPILATION  -I".." -c  
  -o "../bin.v2/libs/unordered/test/compile_map.test/gcc-8/debug/address-model-64/threading-multi/visibility-hidden/unordered/compile_map.o"  
  "../libs/unordered/test/unordered/compile_map.cpp"  
```  
 **Fedora 31 / Intel - GCC 9.3.1  : KO !!**  
```   
    "g++"  -fvisibility-inlines-hidden -fPIC -m64 -pthread -O0 -fno-inline -Wall -g -fvisibility=hidden -pedantic -Wstrict-aliasing -fstrict-aliasing -Wextra -Wsign-promo -Wunused-parameter -Wconversion -Wfloat-equal -Wshadow -DBOOST_ALL_NO_LIB=1 -DBOOST_BEAST_ALLOW_DEPRECATED -DBOOST_BEAST_SEPARATE_COMPILATION -DBOOST_UNORDERED_USE_ALLOCATOR_TRAITS=0  -I".." -c -o "../bin.v2/libs/unordered/test/compile_map_unordered_allocator.test/gcc-9/debug/threading-multi/visibility-hidden/unordered/compile_map.o" "../libs/unordered/test/unordered/compile_map.cpp"  
  
...  
 ../libs/unordered/test/unordered/compile_map.cpp:143:44:   required from here  
  /usr/include/c++/9/ext/new_allocator.h:145:20: error:  
  use of deleted function ‘constexpr std::tuple<_Elements>::tuple(const std::tuple<_Elements>&) [with _Elements = {int&&}]’  
  145 |  noexcept(noexcept(::new((void *)__p)  
      |                    ^~~~~~~~~~~~~~~~~~  
  146 |        _Up(std::forward<_Args>(__args)...)))  
      |        ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
   
 ...failed gcc.compile.c++ ../bin.v2/libs/unordered/test/compile_map_unordered_allocator.test/gcc-9/debug/threading-multi/visibility-hidden/unordered/compile_map.o...  
```

---

## Comment 22

> Username: trex58  
> Created at: 2020-04-30 14:47:55 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/220#issuecomment-621901140  

> I think I may just have to install Fedora and try locally, which Fedora version are you using?  
  
It looks like GCC v9 (and soon GCC v10) has a different behavior than previous versions of GCC. Including bugs and including changes that may break Boost code.  
So, testing Boost with GCC v9 (and soon GCC v10) should be the main goal. Thus you could test on any Linux system providing GCC v9 ; not only Fedora.  
However, Fedora is my favorite Linux environment...  
  
So, please, update GCC on some of your buildfarm platforms, or add to your Buildfarm a Fedora31 (GCC 9.3.1) and/or Fedora32 (GCC 10) environment, that you'll have to continuously upgrade to new versions of Fedora (and of GCC).

---

## Comment 23

> Username: jwakely  
> Created at: 2020-04-30 15:34:38 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/220#issuecomment-621930372  

Yes it's rather shocking that the 1.73.0 release notes say Boost is not tested with any version of GCC less than 2 years old.

---

## Comment 24

> Username: jzmaddock  
> Created at: 2020-04-30 15:50:31 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/220#issuecomment-621939722  

>Yes it's rather shocking that the 1.73.0 release notes say Boost is not tested with any version of GCC less than 2 years old.  
  
That's probably an oversight I would imagine.  
  
As far as multiprecision is concerned, we certainly do test with gcc-9.  Probably to @jwakely 's annoyance I deliberately don't test with gcc pre-release versions as it tend to result in too much tail-chasing after things that get fixed before the release anyway...

---

## Comment 25

> Username: jwakely  
> Created at: 2020-04-30 16:10:10 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/220#issuecomment-621951397  

And I stopped testing pre-release versions of Boost with GCC trunk some years ago, so now nobody is doing that tedious but useful work to find problems before they make it into a release :cry:   
  
Anyway, the `libs/unordered/test/unordered/compile_map.cpp` failure mentioned above (which only happens with `-DBOOST_UNORDERED_USE_ALLOCATOR_TRAITS=0`) is https://gcc.gnu.org/PR89510 and will be fixed in GCC 9.4 and 10.1

---

## Comment 26

> Username: jzmaddock  
> Created at: 2020-04-30 18:26:36 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/220#issuecomment-622023868  

As I'm not familiar with spec files, can one of you confirm the flags passed to b2 when building and testing?  It looks like nothing but -j4?

---

## Comment 27

> Username: jzmaddock  
> Created at: 2020-05-01 17:17:20 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/220#issuecomment-622477899  

OK, I'm struggling to reproduce with fedora 32 and gcc-10.  
  
I'm using a "vanilla" install of boost-1.73 pulled down via git, no patches applied.  
  
I'm also only testing regex, log and timer as these seem to be the ones with linker errors?  
  
I'm pretty sure there's some magic command line option to g++ that's causing the issue, but at present I don't know what. @trex58 are you able to discover what the options being passed to g++ are?  I note that the patches referenced by @jwakely basically remove all the command line options that Boost.Build would normally inject, so I suspect the options are coming from elsewhere (environment variable?) but don't see what they are from scanning the .spec file.

---

## Comment 28

> Username: jwakely  
> Created at: 2020-05-01 17:34:42 UTC  
> Updated at: 2020-05-01 17:35:18 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/220#issuecomment-622484765  

The upstream default flags are replaced with the contents of `RPM_OPT_FLAGS` and `RPM_LD_FLAGS` which the RPM build process adds to the environment, but there's nothing magical about them. I think this is what F32 uses:  
  
```  
RPM_OPT_FLAGS='-O2 -g -pipe -Wall -Werror=format-security -Wp,-D_FORTIFY_SOURCE=2 -Wp,-D_GLIBCXX_ASSERTIONS -fexceptions -fstack-protector-strong -grecord-gcc-switches -specs=/usr/lib/rpm/redhat/redhat-hardened-cc1 -specs=/usr/lib/rpm/redhat/redhat-annobin-cc1 -m64 -mtune=generic -fasynchronous-unwind-tables -fstack-clash-protection -fcf-protection'  
RPM_LD_FLAGS='-Wl,-z,relro -Wl,--as-needed -Wl,-z,now -specs=/usr/lib/rpm/redhat/redhat-hardened-ld'  
```  
  
And for boost I add `-fno-strict-aliasing -Wno-unused-local-typedefs -Wno-deprecated-declarations`  
  
Those shouldn't cause any linker errors, and you should be able to leave most of them out and just use `-O2 -g -pipe -Wall -Werror=format-security -Wp,-D_FORTIFY_SOURCE=2 -Wp,-D_GLIBCXX_ASSERTIONS` for a similar outcome.  
  
Which failures are you trying to reproduce?

---

## Comment 29

> Username: jzmaddock  
> Created at: 2020-05-01 18:36:07 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/220#issuecomment-622509098  

Thanks @jwakely   
  
>Which failures are you trying to reproduce?  
  
The linker errors referenced above, for example:  
  
```  
 /usr/bin/ld: ../bin.v2/libs/log/build/gcc-9.3.1/debug/threadapi-pthread/threading-multi/visibility-hidden/libboost_log_setup.so.1.70.0:  
                 undefined reference to `boost::re_detail_107000::perl_matcher<wchar_t const*, std::allocator<boost::sub_match<wchar_t const*> >, boost::regex_traits<wchar_t, boost::cpp_regex_traits<wchar_t> > >::construct_init(boost::basic_regex<wchar_t, boost::regex_traits<wchar_t, boost::cpp_regex_traits<wchar_t> > > const&, boost::regex_constants::_match_flags)'  
```

---

## Comment 30

> Username: trex58  
> Created at: 2020-05-04 07:38:45 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/220#issuecomment-623307250  

> @trex58 are you able to discover what the options being passed to g++ are?  
  
Hi. 1st of May is a bank holliday in France.  
  
The following talks about my experiments on my Fedora31/x86_64 machine for running Boost 1.73.0 tests.  
  
Since the .spec file of Fedora does not run the tests, I run Boost verification in 2 phases. First, I use the Fedora .spec file for building Boost. Second, I run Boost tests by means of a shell.  
  
The options passed to g++ during the build of Boost were:  
```  
 CFLAGS=' -O2 -g -pipe -Wall -Werror=format-security -Wp,-D_FORTIFY_SOURCE=2 -Wp,-D_GLIBCXX_ASSERTIONS -fexceptions -fstack-protector-strong -grecord-gcc-switches -specs=/usr/lib/rpm/redhat/redhat-hardened-cc1 -specs=/usr/lib/rpm/redhat/redhat-annobin-cc1 -m64 -mtune=generic -fasynchronous-unwind-tables -fstack-clash-protection -fcf-protection'  
```  
and then b2 was called ("build serial") as:  
```  
+ ./b2 -d+2 -q -j4 --without-mpi --without-graph_parallel --build-dir=serial variant=release threading=multi debug-symbols=on pch=off python=2.7 stage  
```  
  
The options passed to g++ when running tests are:   
```  
    g++  -fvisibility-inlines-hidden -fPIC -m64 -pthread -O0 -fno-inline -Wall -g -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1 -DBOOST_BEAST_ALLOW_DEPRECATED -DBOOST_BEAST_SEPARATE_COMPILATION  -I..  
```  
It looks like I should re-run the tests with the -O2 option.  
Anyway, my experiment generated 351 failures during gcc.link, due to about 6 different cases:  
```  
145:  
      /usr/bin/ld: ../bin.v2/libs/timer/build/gcc-9/debug/threading-multi/visibility-hidden/libboost_timer.so.1.73.0:  
                   undefined reference to `boost::chrono::steady_clock::now()'  
 99:  
     /usr/bin/ld: ../bin.v2/libs/coroutine/build/gcc-9/debug/threadapi-pthread/threading-multi/visibility-hidden/libboost_coroutine.so.1.73.0:  
                  undefined reference to `boost::thread_detail::rollback_once_region(boost::once_flag&)'  
 65:  
      /usr/bin/ld: ../bin.v2/libs/graph/build/gcc-9/debug/threading-multi/visibility-hidden/libboost_graph.so.1.73.0:  
                   undefined reference to `boost::basic_regex<char, boost::regex_traits<char, boost::cpp_regex_traits<char> > >::do_assign(char const*, char const*, unsigned int)'  
 23:  
      /usr/bin/ld: ../bin.v2/libs/locale/build/gcc-9/debug/threadapi-pthread/threading-multi/visibility-hidden/libboost_locale.so.1.73.0:  
                   undefined reference to `boost::detail::set_tss_data(void const*, void (*)(void (*)(void*), void*), void (*)(void*), void*, bool)'  
 17:  
      /usr/bin/ld: ../bin.v2/libs/coroutine/build/gcc-9/debug/threadapi-pthread/threading-multi/visibility-hidden/libboost_coroutine.so.1.73.0:  
                   undefined reference to `boost::thread_detail::enter_once_region(boost::once_flag&)'  
  1:  
      /usr/bin/ld: ../bin.v2/libs/type_erasure/build/gcc-9/debug/threadapi-pthread/threading-multi/visibility-hidden/libboost_type_erasure.so.1.73.0:  
                   undefined reference to `boost::this_thread::disable_interruption::disable_interruption()'  
```  
and I got 16 failures at gcc.compile.c++ time:  
```  
../boost/hana/experimental/type_name.hpp:40:21: error: ‘pretty_function’ was not declared in this scope; did you mean ‘Metafunction’?  
  40 |             return {pretty_function + prefix_size, total_size - prefix_size - suffix_size};  
     |                     ^  
../bin.v2/libs/hana/test/test~experimental~type_name.test/gcc-9/debug/threading-multi/visibility-hidden/experimental/type_name.o..  
../libs/hof/test/apply.cpp:444:19: error: static assertion failed  
  444 |     static_assert(noexcept(boost::hof::apply(&no_throw_member_fun::foo_nullary, obj)), "");  
      |                   ^  
../bin.v2/libs/hof/test/apply.test/gcc-9/debug/threading-multi/visibility-hidden/apply.o...  
 ../libs/local_function/test/add_classifiers.cpp:10:5: error: #error "auto-declarations not allowed (using `auto` as storage classifier)"  
  10 | #   error "auto-declarations not allowed (using `auto` as storage classifier)"  
     |     ^  
../bin.v2/libs/local_function/test/add_classifiers.test/gcc-9/debug/threading-multi/visibility-hidden/add_classifiers.o...  
../libs/local_function/test/add_classifiers_seq.cpp:10:5: error: #error "auto-declarations not allowed (using `auto` as storage classifier)"  
  10 | #   error "auto-declarations not allowed (using `auto` as storage classifier)"  
     |     ^  
../bin.v2/libs/local_function/test/add_classifiers_seq.test/gcc-9/debug/threading-multi/visibility-hidden/add_classifiers_seq.o...  
../libs/local_function/test/add_classifiers_seq.cpp:10:5: error: #error "auto-declarations not allowed (using `auto` as storage classifier)"  
  10 | #   error "auto-declarations not allowed (using `auto` as storage classifier)"  
     |     ^  
../bin.v2/libs/local_function/test/add_classifiers_seq_nova.test/gcc-9/debug/threading-multi/visibility-hidden/add_classifiers_seq_nova.o...  
../boost/numeric/odeint/algebra/detail/extract_value_type.hpp:47:73: error: invalid use of incomplete type ‘struct boost::numeric::odeint::detail::extract_value_type<boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50> >, void>’  
  47 |     typedef typename extract_value_type< typename S::value_type >::type type;  
     |                                                                         ^  
../bin.v2/libs/numeric/odeint/test/runge_kutta_error_concepts.test/gcc-9/debug/link-static/threading-multi/visibility-hidden/runge_kutta_error_concepts.o...  
../boost/numeric/odeint/algebra/detail/extract_value_type.hpp:47:73: error: invalid use of incomplete type ‘struct boost::numeric::odeint::detail::extract_value_type<boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50> >, void>’  
  47 |     typedef typename extract_value_type< typename S::value_type >::type type;  
     |                                                                         ^  
../bin.v2/libs/numeric/odeint/test/runge_kutta_controlled_concepts.test/gcc-9/debug/link-static/threading-multi/visibility-hidden/runge_kutta_controlled_concepts.o...  
../boost/numeric/odeint/algebra/detail/extract_value_type.hpp:47:73: error: invalid use of incomplete type ‘struct boost::numeric::odeint::detail::extract_value_type<boost::multiprecision::number<boost::multiprecision::backends::cpp_dec_float<50> >, void>’  
  47 |     typedef typename extract_value_type< typename S::value_type >::type type;  
     |                                                                         ^  
../bin.v2/libs/numeric/odeint/test/runge_kutta_concepts.test/gcc-9/debug/link-static/threading-multi/visibility-hidden/runge_kutta_concepts.o...  
../libs/numeric/ublas/test/tensor/test_tensor.cpp:225:52: error: ‘is_integral_v’ is not a member of ‘std’; did you mean ‘is_integral’?  
 225 |  using distribution_type = std::conditional_t<std::is_integral_v<value_type>, std::uniform_int_distribution<>, std::uniform_real_distribution<> >;  
     |                                                    ^  
../bin.v2/libs/numeric/ublas/test/tensor/test_tensor.test/gcc-9/debug/threading-multi/visibility-hidden/test_tensor.o...  
../boost/numeric/ublas/tensor/multi_index_utility.hpp:335:13: error: ‘vector’ is not a member of ‘std’  
 335 |        std::vector<std::size_t>{std::get<0>( std::get<R>(array) )+1 ...} ,  
     |             ^  
../bin.v2/libs/numeric/ublas/test/tensor/test_tensor.test/gcc-9/debug/threading-multi/visibility-hidden/test_operators_comparison.o...  
../boost/numeric/ublas/tensor/expression.hpp:148:20: error: ‘struct std::_Bind<std::plus<int>(std::_Placeholder<1>, int)>’ has no member named ‘l’  
 148 |    : e(l.e), op(op.l) {}  
     |                    ^  
../bin.v2/libs/numeric/ublas/test/tensor/test_tensor.test/gcc-9/debug/threading-multi/visibility-hidden/test_expression_evaluation.o...  
../boost/numeric/ublas/tensor/expression.hpp:148:20: error: ‘struct operator/(const boost::numeric::ublas::detail::tensor_expression<T, EL>&, typename T::const_reference) [with T = boost::numeric::ublas::tensor<float, boost::numeric::ublas::basic_column_major<> >; L = boost::numeric::ublas::detail::binary_tensor_expression<boost::numeric::ublas::tensor<float, boost::numeric::ublas::basic_column_major<> >, boost::numeric::ublas::tensor<float, boost::numeric::ublas::basic_column_major<> >, boost::numeric::ublas::tensor<float, boost::numeric::ublas::basic_column_major<> >, operator+(const boost::numeric::ublas::detail::tensor_expression<T, EL>&, const boost::numeric::ublas::detail::tensor_expression<T, ER>&) [with T = boost::numeric::ublas::tensor<float, boost::numeric::ublas::basic_column_major<> >; L = boost::numeric::ublas::tensor<float, boost::numeric::ublas::basic_column_major<> >; R = boost::numeric::ublas::tensor<float, boost::numeric::ublas::basic_column_major<> >]::<lambda(const auto:41&, const auto:42&)> >; typename T::const_reference = const float&]::<lambda(const auto:56&)>’ has no member named ‘l’  
 148 |    : e(l.e), op(op.l) {}  
     |                    ^  
../bin.v2/libs/numeric/ublas/test/tensor/test_tensor.test/gcc-9/debug/threading-multi/visibility-hidden/test_functions.o...  
../boost/numeric/ublas/tensor/expression.hpp:148:20: error: ‘struct operator+(const boost::numeric::ublas::detail::tensor_expression<T, EL>&, typename T::const_reference) [with T = boost::numeric::ublas::tensor<int, boost::numeric::ublas::basic_column_major<>, std::vector<int, std::allocator<int> > >; L = boost::numeric::ublas::tensor<int, boost::numeric::ublas::basic_column_major<>, std::vector<int, std::allocator<int> > >; typename T::const_reference = const int&]::<lambda(const auto:53&)>’ has no member named ‘l’  
 148 |    : e(l.e), op(op.l) {}  
     |                    ^  
../bin.v2/libs/numeric/ublas/test/tensor/test_tensor.test/gcc-9/debug/threading-multi/visibility-hidden/test_operators_arithmetic.o...  
../libs/numeric/ublas/test/tensor/test_algorithms.cpp:260:12: error: ‘is_compound_v’ is not a member of ‘std’; did you mean ‘is_compound’?  
 260 |   if(!std::is_compound_v<value_type>)  
     |            ^  
../bin.v2/libs/numeric/ublas/test/tensor/test_tensor.test/gcc-9/debug/threading-multi/visibility-hidden/test_algorithms.o...  
../libs/numeric/ublas/test/tensor/test_tensor_matrix_vector.cpp:423:1:   required from here  
../boost/numeric/ublas/tensor/expression.hpp:148:20: error: ‘struct operator*(typename T::const_reference, const boost::numeric::ublas::detail::tensor_expression<T, EL>&) [with T = boost::numeric::ublas::tensor<int, boost::numeric::ublas::basic_column_major<>, std::vector<int, std::allocator<int> > >; R = boost::numeric::ublas::tensor<int, boost::numeric::ublas::basic_column_major<>, std::vector<int, std::allocator<int> > >; typename T::const_reference = const int&]::<lambda(const auto:51&)>’ has no member named ‘l’  
 148 |    : e(l.e), op(op.l) {}  
     |                 ^  
../bin.v2/libs/numeric/ublas/test/tensor/test_tensor.test/gcc-9/debug/threading-multi/visibility-hidden/test_tensor_matrix_vector.o...  
/usr/include/c++/9/ext/new_allocator.h:145:20: error: use of deleted function ‘constexpr std::tuple<_Elements>::tuple(const std::tuple<_Elements>&) [with _Elements = {int&&}]’  
 145 |  noexcept(noexcept(::new((void *)__p)  
     |                    ^  
 146 |        _Up(std::forward<_Args>(__args)...)))  
../bin.v2/libs/unordered/test/compile_map_unordered_allocator.test/gcc-9/debug/threading-multi/visibility-hidden/unordered/compile_map.o...  
```  
  
About Math computation, it looks like the above failures mainly deal with "geometry" library:  
```  
                       passed  failed  skipped  
       math              593      2        0  
       geometry          330    109      109  
       multiprecision    461      0        0  
```  
  
Since I'm focused now on the issues I have when building/testing Boost 1.73 on AIX with GCC 8.4 and GCC 9.3, I wasn't able to take a look at these errors on Fedora/Intel yet.

---

## Comment 31

> Username: jwakely  
> Created at: 2020-05-04 11:13:49 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/220#issuecomment-623402850  

Those local_function and numeric/odeint ones are failing across the board for all compilers, and the numeric/ublas ones are failing for all compilers in C++98, C++11 and C++14 modes (so probably relying on C++17 guaranteed copy elision).  
  
So most of what you're seeing is "expected" (in as much as everybody is seeing those errors):  
https://www.boost.org/development/tests/master/developer/summary.html

---

## Comment 32

> Username: ckormanyos  
> Created at: 2021-02-26 15:33:09 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/220#issuecomment-786718615  

I think we need to re-review the content of this thread, as the entire CI has been completely overhauled for 1.76. Admittedly, I did not review this post entirely, but based on its title and general content, I think some updates might already have been handled.  
  
Restarting discussion on this for post 1.76.
