# #887 fix sha1 digest type [Merged]

> Username: mmitti  
> Created at: 2024-08-19 01:44:53 UTC  
> Updated at: 2024-08-22 07:26:26 UTC  
> Merged at: 2024-08-22 03:42:33 UTC  
> Closed at: 2024-08-22 03:42:33 UTC  
> Url: https://github.com/boostorg/compute/pull/887  

In boost 1.86, changing boost::uuids::detail::sha1::digest_type int[5] to char[20]. I was compile current code with MSVC2022, i got compile errors due to those change. So, I fix to folloup those changes to pass compile.  
  
ref: https://github.com/boostorg/uuid/commit/0f843137a1a479797004f195ec615fdc6ac1c219#diff-8a0985025af60846be9c809585d2659b4e3d5de04714ff76d174b80aaa719c97

---

## Comment 1

> Username: pdimov  
> Created_at: 2024-08-22 07:26:25 UTC  
> Url: https://github.com/boostorg/compute/pull/887#issuecomment-2303968978  

This fix is slightly incorrect, because `digest[i]` is `unsigned char` and is output as a character to `buf`, instead as an integer, as intended.  
  
https://github.com/boostorg/compute/pull/893 fixes it.  
https://github.com/boostorg/compute/pull/892 adds a test for the `sha1` class. The test passes for 1.85, and after https://github.com/boostorg/compute/pull/893.  
  
Additionally, https://github.com/boostorg/compute/pull/891 is a small change to the test Jamfile that makes the tests link under Windows as well.

---
