# #1119 - boost::multiprecision::cpp_int leading zeros parsing bug [Closed]

> Username: ohhmm  
> Created at: 2024-04-15 17:47:46 UTC  
> Updated at: 2025-07-30 07:12:26 UTC  
> Closed at: 2024-04-16 08:03:12 UTC  
> Url: https://github.com/boostorg/math/issues/1119  

boost::multiprecision::cpp_int("000144") parsed into value 100.  
See some details here: [https://github.com/ohhmm/openmind/blob/28b9ed5d55bf1766606442cc34689ee0c32671e7/omnn/math/test/Integer_test.cpp#L59](https://github.com/ohhmm/openmind/blob/28b9ed5d55bf1766606442cc34689ee0c32671e7/omnn/math/test/Integer_test.cpp#L59)

---

## Comment 1

> Username: mborland  
> Created at: 2024-04-16 08:03:12 UTC  
> Url: https://github.com/boostorg/math/issues/1119#issuecomment-2058480541  

Duplicate from multiprecision

---

## Comment 2

> Username: ckormanyos  
> Created at: 2024-04-16 08:21:31 UTC  
> Url: https://github.com/boostorg/math/issues/1119#issuecomment-2058518066  

>`boost::multiprecision::cpp_int("000144")` parsed into value 100.  
  
Should this parse as an octal base?

---

## Comment 3

> Username: ohhmm  
> Created at: 2024-04-16 10:14:17 UTC  
> Url: https://github.com/boostorg/math/issues/1119#issuecomment-2058735002  

Hi. This doesn't look expected to me. But since it is C standard compliant, lets close this ticket.

---

## Comment 4

> Username: rhossack  
> Created at: 2025-07-29 21:48:17 UTC  
> Url: https://github.com/boostorg/math/issues/1119#issuecomment-3134172025  

I ran into this issue trying to do project euler #55. If I try to create a cpp_int from a string that starts with a zero, it throws a wrapexcept.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2025-07-30 07:12:26 UTC  
> Url: https://github.com/boostorg/math/issues/1119#issuecomment-3135121930  

We will need a test case to investigate, but remember that all numbers starting with a 0 are parsed as OCTAL strings.  This is std lib compatible/compliant behaviour.
