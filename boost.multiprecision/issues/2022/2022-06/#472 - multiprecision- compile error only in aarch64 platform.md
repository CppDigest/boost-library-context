# #472 - multiprecision: compile error only in aarch64 platform [Closed]

> Username: timlee66  
> Created at: 2022-06-08 13:05:29 UTC  
> Updated at: 2022-07-12 07:32:43 UTC  
> Closed at: 2022-07-12 07:32:43 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/472  

Hi All,  
We meet one compile error in "aarch64" platform with OpenBMC software system.  
However, previous boost v1.78.0 without this compile error in OpenBMC/ipmid no matter arm or aarch64.  
  
But, the latest boost v1.79.0 meet this compile error in OpenBMC/ipmid only exist aarch64 platform.  
I try to diff v1.78.0 and v1.79.0 to find out the root cause, but there are many changes.  
Thus, we need your help to review this issue symptom why only error in aarch64 platform?  
BTW, OpenBMC/ipmid use the same commit to build on arm and aarch64 platform.  
  
Compile error log as below: (Attachment for complete log information)  
**error: static assertion failed: Attempt to pack a type that has no IPMI pack operation**  
/cortexa35-openbmc-linux/phosphor-ipmi-host/1.0+gitAUTOINC+5d38067181-r1/build/../git/include/ipmid/message/pack.hpp:80:28: error: static assertion failed: Attempt to pack a type that has no IPMI pack operation  
   80 |         static_assert(std::is_integral_v<T>,  
      |                       ~~~~~^~~~~~~~~~~~~~~~  
/cortexa35-openbmc-linux/phosphor-ipmi-host/1.0+gitAUTOINC+5d38067181-r1/build/../git/include/ipmid/message/pack.hpp:80:28: note: 'std::is_integral_v<boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<24, 24, boost::multiprecision::unsigned_magnitude, boost::multiprecision::unchecked, void>, boost::multiprecision::et_off> >' evaluates to false  
  
OpenBMC/ipmid github:  
https://github.com/openbmc/phosphor-host-ipmid/blob/master/include/ipmid/message/pack.hpp  
  
static_assert() in pack.hpp:  
template <typename T>  
struct PackSingle  
{  
    static int op(Payload& p, const T& t)  
    {  
        **static_assert(std::is_integral_v<T>,**  
                      **"Attempt to pack a type that has no IPMI pack operation"**);  
  
Best regards,  
Tim  
[compile_error_log.txt](https://github.com/boostorg/boost/files/8861723/compile_error_log.txt)

---

## Comment 1

> Username: jzmaddock  
> Created at: 2022-06-08 17:30:32 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/472#issuecomment-1150198882  

From what I can see, you're using a Boost.Multiprecision type as uint24_t?  The problem is that a multipreciison type is not a native integer type, so once you get into here: https://github.com/openbmc/phosphor-host-ipmid/blob/c710b975dce80ba877b9d98b40455746fb9132ec/include/ipmid/message/unpack.hpp#L122 the static_assert is quite correctly triggered.  The docs for that type at the start of the file say:  
  
```  
@struct UnpackSingle  
 *  @brief Utility to unpack a single C++ element from a Payload  
 *  
 *  User-defined types are expected to specialize this template in order to  
 *  get their functionality.  
 ```  
  
And as Boost.Multiprecision types are user-defined types, you need to do as the docs say and specialize the template?

---

## Comment 2

> Username: timlee66  
> Created at: 2022-06-09 09:45:35 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/472#issuecomment-1150908657  

> From what I can see, you're using a Boost.Multiprecision type as uint24_t? The problem is that a multipreciison type is not a native integer type, so once you get into here: https://github.com/openbmc/phosphor-host-ipmid/blob/c710b975dce80ba877b9d98b40455746fb9132ec/include/ipmid/message/unpack.hpp#L122 the static_assert is quite correctly triggered. The docs for that type at the start of the file say:  
>   
> ```  
> @struct UnpackSingle  
>  *  @brief Utility to unpack a single C++ element from a Payload  
>  *  
>  *  User-defined types are expected to specialize this template in order to  
>  *  get their functionality.  
> ```  
>   
> And as Boost.Multiprecision types are user-defined types, you need to do as the docs say and specialize the template?  
  
Hi @jzmaddock,  
  
Thanks your help to check this issue. I wonder why we use same ipmid commit build on arm and arrach64, boost v1.78.0 without this kind of compile error. However, this compile error exist on arrach64 when using boost v1.79.0 (https://github.com/openbmc/openbmc/tree/master/poky/meta/recipes-support/boost)  
  
Sorry, I'm not familiar with boost. This issue symptom only happen on arrach64 platform with boost v1.79.0, but v1.78.0. That's why we need all you experts help us to check whether there is any architecture dependence between these two versions then cause this compile error.  
  
About ipmid, I will mail to OpenBMC community help to check this symptom in parallel. Thanks.  
  
Best regards,  
Tim

---

## Comment 3

> Username: jzmaddock  
> Created at: 2022-06-09 16:25:17 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/472#issuecomment-1151345611  

Just to be clear, from what I can see of the code, _this should generate an error on all platforms_ and with all _Boost verisons_.  
  
Rationale:  
  
executeIpmiOemCommand in ipmid-new.cpp calls unpack with a `uint24_t`, and `uint24_t` is defined here https://github.com/openbmc/phosphor-host-ipmid/blob/c710b975dce80ba877b9d98b40455746fb9132ec/include/ipmid/message/types.hpp#L70 as an alias for a multiprecision type.  Then inside the unpack method here: https://github.com/openbmc/phosphor-host-ipmid/blob/c710b975dce80ba877b9d98b40455746fb9132ec/include/ipmid/message/unpack.hpp#L75 the first `if constexpr (std::is_fundamental<T>::value)` must be `false` since multiprecision types are user-defined class types (ie `is_class` would be true, `is_fundamental` and `is_integer` false), the next `else if constexpr (utility::is_tuple<T>::value)` must also be false, which leads us on to the unconditional static_assert at the end.  
  
There are similar issues in the `pack` method as well.  
  
I do notice they changed the build system 10 days ago, I wonder if something that wasn't being built before is now?  
  
Sorry I can't be more help, but I just don't see how that code could ever have compiled with any Boost version.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2022-06-09 17:45:18 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/472#issuecomment-1151419607  

>I do notice they changed the build system 10 days ago, I wonder if something that wasn't being built before is now?  
  
I think this may be the case, looking at the latest commit diff which changed the build system: https://github.com/openbmc/phosphor-host-ipmid/commit/c710b975dce80ba877b9d98b40455746fb9132ec#diff-0462e381b2fb3286568215681c8983490a37ac9ae0f0c5ee304df7fa6426d4af the old Makefile.am only lists the problem file under:  
  
```  
ipmid_SOURCES = \  
	ipmid-new.cpp \  
	settings.cpp \  
	host-cmd-manager.cpp  
```  
  
and then the variable `ipmid_SOURCES` is never used (unlike all the others in there).  
  
I suspect if you wind back OpenBMC by a couple of weeks, you may well find that things do then build?

---

## Comment 5

> Username: timlee66  
> Created at: 2022-06-10 03:49:53 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/472#issuecomment-1151903302  

>   
Hi @jzmaddock,  
Thanks you help to check OpenBMC related ipmid code. Sorry to interrupt you recently.  
  
First, I didn't use the latest commit about ipmid to build on arm and aarch64 platform.  
I use this old commit:  
[5d38067181b376bf487872c73a55c7c9f1089a18](https://github.com/openbmc/phosphor-host-ipmid/commit/5d38067181b376bf487872c73a55c7c9f1089a18)  
  
And this old commit is using automake/autoconf to build, the ipmid_SOURCES will be included in Makefile.in eventually.  
Thus, the ipmid-new.cpp will be inclued to compile.  
  
Makefile.in as below:  
SOURCES = $(libdynamiccmds_la_SOURCES) $(libipmi20_la_SOURCES) \  
	$(libsysintfcmds_la_SOURCES) $(libusercmds_la_SOURCES) \  
	$(libwhitelist_la_SOURCES) $(nodist_libwhitelist_la_SOURCES) \  
	$(**ipmid_SOURCES**)  
  
However, I also use the latest commit of ipmid that using meson build method to compile.  
In meson.build file that also include ipmid-new.cpp to compile.  
  
meson.build as below:  
# ipmid binary  
executable(  
  'ipmid',  
  'ipmid-new.cpp',  
  'host-cmd-manager.cpp',  
  'settings.cpp',  
    
I still meet compile error only in aarch64 platform with boost v1.79.0, but v1.78.0 will build pass.  
Just clarify ipmid-new.cpp both be included to compile in old and latest commit of ipmid. Thanks.

---

## Comment 6

> Username: timlee66  
> Created at: 2022-06-10 08:13:36 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/472#issuecomment-1152097356  

> From what I can see, you're using a Boost.Multiprecision type as uint24_t? The problem is that a multipreciison type is not a native integer type, so once you get into here: https://github.com/openbmc/phosphor-host-ipmid/blob/c710b975dce80ba877b9d98b40455746fb9132ec/include/ipmid/message/unpack.hpp#L122 the static_assert is quite correctly triggered. The docs for that type at the start of the file say:  
>   
> ```  
> @struct UnpackSingle  
>  *  @brief Utility to unpack a single C++ element from a Payload  
>  *  
>  *  User-defined types are expected to specialize this template in order to  
>  *  get their functionality.  
> ```  
>   
> And as Boost.Multiprecision types are user-defined types, you need to do as the docs say and specialize the template?  
  
Actually, ipmid already specialized lots of template for user-defined types. For example, uint24_t will use this template template <unsigned N> struct UnpackSingle<fixed_uint_t<N>>  
  
https://github.com/openbmc/phosphor-host-ipmid/blob/c710b975dce80ba877b9d98b40455746fb9132ec/include/ipmid/message/unpack.hpp#L161

---

## Comment 7

> Username: jzmaddock  
> Created at: 2022-06-14 18:18:25 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/472#issuecomment-1155536771  

>Actually, ipmid already specialized lots of template for user-defined types. For example, uint24_t will use this template template struct UnpackSingle<fixed_uint_t>  
  
Ahhh, good catch, my mistake.  None the less the error message instantiation-backtrace clearly shows that the primary template of `unpack` is being found and not the specialization.  I have no answer for that, and there are no changes in the interfaces of the multiprecision templates either.  So I'm stuck.  
  
What are the compiler versions being used on the 2 platforms, could this be a compiler bug?

---

## Comment 8

> Username: timlee66  
> Created at: 2022-06-15 02:26:17 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/472#issuecomment-1155910696  

> > Actually, ipmid already specialized lots of template for user-defined types. For example, uint24_t will use this template template struct UnpackSingle<fixed_uint_t>  
>   
> Ahhh, good catch, my mistake. None the less the error message instantiation-backtrace clearly shows that the primary template of `unpack` is being found and not the specialization. I have no answer for that, and there are no changes in the interfaces of the multiprecision templates either. So I'm stuck.  
>   
> What are the compiler versions being used on the 2 platforms, could this be a compiler bug?  
  
Thanks your help to discuss this issue symptom with me. I'm very appreciate.  
I'm also stuck. Because, if this is relate to compiler version, why same build environment will have different compile result only change boost version from 1.78.0 to 1.79.0?  
  
In the same build environment, I just copy all files from boost 1.78.0 to replace boost 1.79.0.  
boost path is https://github.com/openbmc/openbmc/tree/master/poky/meta/recipes-support/boost  
  
Below is the difference between these two versions, I just found that there are two patches in 1.78.0, but 1.79.0. However, I found that those two patches already include in 1.79.0. Thus, boost_1.79.0.bb file didn't need apply these two patches. You can refer it here. https://github.com/openbmc/openbmc/blob/master/poky/meta/recipes-support/boost/boost_1.79.0.bb  
  
![image](https://user-images.githubusercontent.com/26107026/173716466-790cd6f3-2a5a-4cad-8a40-fa699a914aab.png)  
  
However, the source code between 1.78.0 and 1.79.0 seems lots of file changes, not just those two patches. I try to compare all file changed, but too many files be changed, I don't have idea which file or which commit change might be caused this compile error in aarch64. I still try to find out any clues, but :(  
  
The compile information you can check previous I attach compile_error_log.txt and there are many -DBOOST_XXX compile options, might be those compile options will go to different code flow when platform is aarch64. I know this issue is very tough to clarify, hope I can provide more clues to you. Thanks.  
  
make[2]: Entering directory '/home/tim/npcm-master/openbmc/build/evb-npcm845/tmp/work/cortexa35-openbmc-linux/phosphor-ipmi-host/1.0+gitAUTOINC+5d38067181-r1/build'  
aarch64-openbmc-linux-g++  -mcpu=cortex-a35 -march=armv8-a+crc -fstack-protector-strong  -O2 -D_FORTIFY_SOURCE=2 -Wformat -Wformat-security -Werror=format-security --sysroot=/home/tim/npcm-master/openbmc/build/evb-npcm845/tmp/work/cortexa35-openbmc-linux/phosphor-ipmi-host/1.0+gitAUTOINC+5d38067181-r1/recipe-sysroot -std=c++20 -DHAVE_CONFIG_H -I. -I../git  -I/home/tim/npcm-master/openbmc/build/evb-npcm845/tmp/work/cortexa35-openbmc-linux/phosphor-ipmi-host/1.0+gitAUTOINC+5d38067181-r1/build/../git -I/home/tim/npcm-master/openbmc/build/evb-npcm845/tmp/work/cortexa35-openbmc-linux/phosphor-ipmi-host/1.0+gitAUTOINC+5d38067181-r1/build/../git/include -DENABLE_BOOT_FLAG_SAFE_MODE_SUPPORT -DFEATURE_SEL_LOGGER_CLEARS_SEL -flto -Wno-psabi   **-DBOOST_ASIO_DISABLE_THREADS -DBOOST_ALL_NO_LIB -DBOOST_SYSTEM_NO_DEPRECATED -DBOOST_ERROR_CODE_HEADER_ONLY -DBOOST_COROUTINES_NO_DEPRECATION_WARNING -DBOOST_ASIO_DISABLE_THREADS -DBOOST_ALL_NO_LIB -DBOOST_SYSTEM_NO_DEPRECATED -DBOOST_ERROR_CODE_HEADER_ONLY -DBOOST_COROUTINES_NO_DEPRECATION_WARNING -DBOOST_ASIO_DISABLE_THREADS -DBOOST_ALL_NO_LIB -DBOOST_SYSTEM_NO_DEPRECATED -DBOOST_ERROR_CODE_HEADER_ONLY -DBOOST_COROUTINES_NO_DEPRECATION_WARNING -DBOOST_ERROR_CODE_HEADER_ONLY -DBOOST_SYSTEM_NO_DEPRECATED -DBOOST_COROUTINES_NO_DEPRECATION_WARNING -DBOOST_ASIO_DISABLE_THREADS -DBOOST_ALL_NO_LIB** -O2 -pipe -g -feliminate-unused-debug-types -fmacro-prefix-map=/home/tim/npcm-master/openbmc/build/evb-npcm845/tmp/work/cortexa35-openbmc-linux/phosphor-ipmi-host/1.0+gitAUTOINC+5d38067181-r1=/usr/src/debug/phosphor-ipmi-host/1.0+gitAUTOINC+5d38067181-r1                      -fdebug-prefix-map=/home/tim/npcm-master/openbmc/build/evb-npcm845/tmp/work/cortexa35-openbmc-linux/phosphor-ipmi-host/1.0+gitAUTOINC+5d38067181-r1=/usr/src/debug/phosphor-ipmi-host/1.0+gitAUTOINC+5d38067181-r1                      -fdebug-prefix-map=/home/tim/npcm-master/openbmc/build/evb-npcm845/tmp/work/cortexa35-openbmc-linux/phosphor-ipmi-host/1.0+gitAUTOINC+5d38067181-r1/recipe-sysroot=                      -fdebug-prefix-map=/home/tim/npcm-master/openbmc/build/evb-npcm845/tmp/work/cortexa35-openbmc-linux/phosphor-ipmi-host/1.0+gitAUTOINC+5d38067181-r1/recipe-sysroot-native=  -fvisibility-inlines-hidden -Wall -Werror -c -o ipmid-ipmid-new.o `test -f 'ipmid-new.cpp' || echo '../git/'`ipmid-new.cpp  
aarch64-openbmc-linux-g++  -mcpu=cortex-a35 -march=armv8-a+crc -fstack-protector-strong  -O2 -D_FORTIFY_SOURCE=2 -Wformat -Wformat-security -Werror=format-security --

---

## Comment 9

> Username: timlee66  
> Created at: 2022-06-15 08:06:23 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/472#issuecomment-1156134668  

Hi jzmaddock,  
I have attached compile logs for boost 1.78 and 1.79. You can refer it. Thanks.  
  
And I also attached compile logs for ipmid   
(ipmid_with_boost_178_log.do_compile is compile pass with boost 1.78,   
 ipmid_with_boost_179_log.do_compile is compile error with boost 1.79).  
  
I hope those compile information can help to clarify this issue. I also dig these logs.  
  
Sincerely,  
Tim  
[debug_log.zip](https://github.com/boostorg/multiprecision/files/8907142/debug_log.zip)

---

## Comment 10

> Username: jzmaddock  
> Created at: 2022-06-16 17:57:31 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/472#issuecomment-1157970997  

Found it!  Unfortunately I'm not sure what we can do about it :(  
  
We changed all bit counts from `unsigned` to `std::size_t`, specifically for platforms like arm64 (and windows!) where `unsigned` is narrower than `size_t` so that the maximum representable number isn't unnecessarily constrained.  This then changes the interface for `cpp_int_backend` to use size_t rather than unsigned for the bit counts.  On most platforms and most use cases, this makes no perceptible difference, but unfortunately this appears to be one situation where it really does make a difference.  Apparently this is true even though:  
  
```  
template <unsigned N>  
using fixed_uint_t =  
    boost::multiprecision::number<boost::multiprecision::cpp_int_backend<  
        N, N, boost::multiprecision::unsigned_magnitude,  
        boost::multiprecision::unchecked, void>>;  
```  
  
Is declared with an unsigned parameter, when partially specializing for `fixed_uint_t` you need to use the actual type of the template parameter in the underlying template, not the type used in the template alias! :(  
  
I'll try and work up a PR for ipmid shortly.

---

## Comment 11

> Username: jzmaddock  
> Created at: 2022-06-16 18:15:57 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/472#issuecomment-1157992620  

Please see https://github.com/openbmc/phosphor-host-ipmid/pull/183 for a possible fix.

---

## Comment 12

> Username: timlee66  
> Created at: 2022-06-17 02:13:28 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/472#issuecomment-1158400669  

Hi @jzmaddock,  
  
Great!!! You are right, there are many difference is use std::size_t instead of unsigned type between 1.78 and 1.79 when I compare these two versions especially at boost/multiprecision folder.  
However, I don't know the reason why need change unsigned to std::size_t.  
  
I will check this possible fix and run ipmitool command to make sure the functionality of ipmid.  
BTW, the fix need to submit to OpenBMC gerrit then ipmid owner will help to review and merge it.  
If you need help, please don't hesitate tell me. Thanks a lot.  
  
Best regards,  
Tim

---

## Comment 13

> Username: jzmaddock  
> Created at: 2022-06-17 18:34:56 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/472#issuecomment-1159137658  

Are you able to submit this patch via gerrit?  I just can't seem to get things working from here.

---

## Comment 14

> Username: timlee66  
> Created at: 2022-06-17 18:40:18 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/472#issuecomment-1159141499  

> Are you able to submit this patch via gerrit? I just can't seem to get things working from here.  
  
OK. I can help to submit this patch via gerrit.  
BTW, I have tested the patch and no more compile error in aarch64 platform.

---

## Comment 15

> Username: timlee66  
> Created at: 2022-06-29 03:02:36 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/472#issuecomment-1169482295  

Hello John,  
  
Is this compile error can be solved in boost next release? Is this possible?  
I just think that might be more applications on OpenBMC using same unsigned type not std::size_t as ipmid,  
then got same compile error when building aarch64 platform.  
  
However, the commit already submit to OpenBMC/ipmid gerrit as below:  
Until now, we still cannot see the maintainer reply any suggestion and comment.  
https://gerrit.openbmc.org/c/openbmc/phosphor-host-ipmid/+/54692  
  
Best regards,  
Tim

---

## Comment 16

> Username: jzmaddock  
> Created at: 2022-06-29 11:22:33 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/472#issuecomment-1169861041  

It definitely won't be fixed in the next Boost release (for which a beta is due very soon), but in general, I don't believe we *can* fix this at our end without reverting the change to use std::size_t for bit counts - something we probably should *not* do.

---

## Comment 17

> Username: timlee66  
> Created at: 2022-06-30 09:12:29 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/472#issuecomment-1170968797  

> It definitely won't be fixed in the next Boost release (for which a beta is due very soon), but in general, I don't believe we _can_ fix this at our end without reverting the change to use std::size_t for bit counts - something we probably should _not_ do.  
  
Thanks your reply. I think revert all changes about change unsigned to std::size_t might be have some risks.  
Actually, I don't know the reason why need change unsigned to std::size_t for lots of files. Could I know the reason?  
  
Seems bit counts will have different behavior when change to size_t, is it possible to use unsigned instead of size_t only for bit counts and keep the other files using size_t? As you mentioned before, unsigned is narrower than size_t, specifically for platforms like arm64. That's why I think keep using unsigned type only for bit counts. Thanks.

---

## Comment 18

> Username: timlee66  
> Created at: 2022-07-11 01:05:39 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/472#issuecomment-1179848610  

Hi John,  
Another question need your help.  
OpenBMC ipmid community ask one question about boost version check.  
Could you help me to check this? Thank you.  
  
Tom Joseph said that.  
I read the commit message, wanted to confirm that this cannot be size_t for BOOST_VERSION < 107900. I curious if we can avoid the BOOST_VERSION check.  
  
Best regards,  
Tim

---

## Comment 19

> Username: jzmaddock  
> Created at: 2022-07-11 08:23:08 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/472#issuecomment-1180107161  

Correct: the correct type for the template is unsigned prior to boost-1.79 and size_t from 1.79 and later.  Of course many/most platforms have size_t=unsigned which is why this didn't show up before.  So yes, the version check is necessary.

---

## Comment 20

> Username: timlee66  
> Created at: 2022-07-11 08:32:49 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/472#issuecomment-1180116484  

Thanks your explanation for this question. I will reply to OpenBMC community.  
If there is any update, I will let  you know. Thank you again.  
  
Best regards,  
Tim

---

## Comment 21

> Username: timlee66  
> Created at: 2022-07-12 06:16:10 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/472#issuecomment-1181359071  

Hi John,  
The commit on openbmc/phosphor-host-ipmid already be merged it. Thanks your help to resolve it.  
  
Best regards,  
Tim
