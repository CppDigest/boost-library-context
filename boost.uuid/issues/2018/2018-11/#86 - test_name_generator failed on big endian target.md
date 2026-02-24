# #86 - test_name_generator failed on big endian target [Closed]

> Username: BinCaoWR  
> Created at: 2018-11-12 09:25:56 UTC  
> Updated at: 2019-06-25 17:21:16 UTC  
> Closed at: 2019-06-25 17:21:16 UTC  
> Url: https://github.com/boostorg/uuid/issues/86  

Test log:  
../libs/uuid/test/test_name_generator.cpp(94): test 'md == correct_md5' failed in function 'int main(int, char**)': '06205cec-255b-300e-a8bc-a8605ab8244e' != 'ec5c2006-0e00-3b25-a0a8-bce84e24b85a'  
1 error detected.  
  
Potential solution:  
Update boost/uuid/detail/md5.hpp with followings:  
1. Add the following code:  
#if defined(__i386__) || defined(__x86_64__) || defined(__vax__)  
    #define BOOST_UUID_DETAIL_MD5_OUT_NEW(dst, src) \  
        (dst)[0] = (unsigned char)(src); \  
        (dst)[1] = (unsigned char)((src) >> 8); \  
        (dst)[2] = (unsigned char)((src) >> 16); \  
        (dst)[3] = (unsigned char)((src) >> 24);  
#else  
#define BOOST_UUID_DETAIL_MD5_OUT_NEW(dst, src) \  
	(dst)[0] = (unsigned char)((src) >> 24); \  
	(dst)[1] = (unsigned char)((src) >> 16); \  
	(dst)[2] = (unsigned char)((src) >> 8); \  
	(dst)[3] = (unsigned char)(src);  
#endif  
2. Update the following code in MD5_Final() to use the new macro:  
From:  
        BOOST_UUID_DETAIL_MD5_OUT(&result[0], ctx->a)  
        BOOST_UUID_DETAIL_MD5_OUT(&result[4], ctx->b)  
        BOOST_UUID_DETAIL_MD5_OUT(&result[8], ctx->c)  
        BOOST_UUID_DETAIL_MD5_OUT(&result[12], ctx->d)  
To:  
        BOOST_UUID_DETAIL_MD5_OUT_NEW(&result[0], ctx->a)  
        BOOST_UUID_DETAIL_MD5_OUT_NEW(&result[4], ctx->b)  
        BOOST_UUID_DETAIL_MD5_OUT_NEW(&result[8], ctx->c)  
        BOOST_UUID_DETAIL_MD5_OUT_NEW(&result[12], ctx->d)  
  
Verified the above solution works.

---

## Comment 1

> Username: Lastique  
> Created at: 2018-11-12 11:31:31 UTC  
> Url: https://github.com/boostorg/uuid/issues/86#issuecomment-437847390  

FYI, we have `boost/predef/other/endian.h` to detect endianness.

---

## Comment 2

> Username: jeking3  
> Created at: 2018-11-12 11:33:16 UTC  
> Updated at: 2018-11-12 13:13:48 UTC  
> Url: https://github.com/boostorg/uuid/issues/86#issuecomment-437847823  

Thanks for the report.  I will look at determining endianness through  
Boost.Predef and adjust accordingly.  If you would like to submit a pull request, please do.

---

## Comment 3

> Username: BinCaoWR  
> Created at: 2018-11-13 08:46:48 UTC  
> Url: https://github.com/boostorg/uuid/issues/86#issuecomment-438183935  

Thanks for the comments. Here is my analysis on this issue:  
MD5_Final() from md5.hpp save a 4 bytes value(such as ctx->a) into 4 unsigned char variables, then hash_to_uuid() from basic_name_generator.hpp read the value 4 bytes at a time.   
For example, in MD5_Final(), if ctx->a is 0x12345678, then result[0] would be 0x78, result[1] would be 0x56, result[3] would be 0x34, result[4] would be 0x12. Then in hash_to_uuid(), digest[0] would be 0x78563412 on big endian. So the uuid would not be correct on big endian.  
  
We can update either MD5_Final() or hash_to_uuid() to consider the endian. However if we updated hash_to_uuid(), the code in get_digest() from sha1.hpp which directly save 4 bytes value would have problem.  
  
So my pull request updated md5.hpp to make the 4 bytes value correct, which is aligned with sha1.hpp.

---

## Comment 4

> Username: BinCaoWR  
> Created at: 2018-11-13 08:47:53 UTC  
> Url: https://github.com/boostorg/uuid/issues/86#issuecomment-438184259  

Here is my pull request:  
https://github.com/boostorg/uuid/pull/87

---

## Comment 5

> Username: Lastique  
> Created at: 2018-11-13 09:01:14 UTC  
> Url: https://github.com/boostorg/uuid/issues/86#issuecomment-438188095  

UUID generation from name is specified in https://tools.ietf.org/html/rfc4122#section-4.3 and it describes how octets of the hash should be used.

---

## Comment 6

> Username: jeking3  
> Created at: 2018-11-13 13:48:22 UTC  
> Url: https://github.com/boostorg/uuid/issues/86#issuecomment-438271577  

Per my comment on the PR, it's likely that the string generator code or the test code is to blame and not the name generator.  The name generator is performing to the RFC which states octets 0 through 3 are copied in directly; however in the string generator or in the test I could see this might be an issue.  It's a good find, however changing name_generator doesn't seem like the right place to fix it at the moment.  I'll have to look at it some more though.  I only briefly scanned the issue so far.

---

## Comment 7

> Username: BinCaoWR  
> Created at: 2018-11-23 08:11:52 UTC  
> Url: https://github.com/boostorg/uuid/issues/86#issuecomment-441175947  

I'm sorry, I'm not familiar with this. My understanding is, the byte order convert should be done before set variant & version in hash_to_uuid() from basic_name_generator.hpp, otherwise the final uuid would be different for little and big endians.  
In https://tools.ietf.org/html/rfc4122#appendix-A , the byte order convert is done in format_uuid_v3or5(). Does that mean we should convert the byte order in hash_to_uuid() ?

---

## Comment 8

> Username: jeking3  
> Created at: 2018-12-20 18:41:50 UTC  
> Url: https://github.com/boostorg/uuid/issues/86#issuecomment-449096127  

Other projects like Azure/WALinuxAgent have experienced endianness issues as well.  In fact I recently dug into a case someone posted about ESXi where upgrading to ESXi 6.5 changed the uuid of all the VMs in vCenter, where all the reported UUIDs went through an endian change.  So, yes, endianness is a confusing issue when it comes to UUIDs.  I'm fairly sure any endianness issues that occur are going to be to and from string representation.

---

## Comment 9

> Username: jeking3  
> Created at: 2019-06-08 10:33:03 UTC  
> Url: https://github.com/boostorg/uuid/issues/86#issuecomment-500113845  

On a more thorough review (sorry it took so long) I see the string generator always works with the raw bytes and is not affected by endianness.  It would be the generator at fault as the test that failed pointed this out.  This test failing means that the uuid result (raw or string) on different endian platforms would be a different value and that is not correct, therefore I agree with the original defect assessment.

---

## Comment 10

> Username: jeking3  
> Created at: 2019-06-11 16:01:26 UTC  
> Url: https://github.com/boostorg/uuid/issues/86#issuecomment-500909096  

I'm still looking into this.
