# #612 - boost::multiprecision::cpp_int leading zeros parsing bug [Closed]

> Username: ohhmm  
> Created at: 2024-04-15 17:48:51 UTC  
> Updated at: 2024-04-16 10:15:32 UTC  
> Closed at: 2024-04-16 10:15:31 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/612  

boost::multiprecision::cpp_int("000144") parsed into value 100.  
See some details here: https://github.com/ohhmm/openmind/blob/28b9ed5d55bf1766606442cc34689ee0c32671e7/omnn/math/test/Integer_test.cpp#L59

---

## Comment 1

> Username: ckormanyos  
> Created at: 2024-04-16 08:22:14 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/612#issuecomment-2058519471  

>`boost::multiprecision::cpp_int("000144")` parsed into value 100.  
  
Should this parse as an octal base?

---

## Comment 2

> Username: mborland  
> Created at: 2024-04-16 08:26:07 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/612#issuecomment-2058526587  

> > `boost::multiprecision::cpp_int("000144")` parsed into value 100.  
>   
> Should this parse as an octal base?  
  
I think anything of the form 0144 should be octal, but 000144 are base-10 leading zeros that need to get stripped out. See the linked PR.

---

## Comment 3

> Username: ckormanyos  
> Created at: 2024-04-16 08:29:35 UTC  
> Updated at: 2024-04-16 08:30:24 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/612#issuecomment-2058533010  

> as an octal base?  
  
Because then the expected answer is: $8{\times}8+4{\times}8+4=100$

---

## Comment 4

> Username: ohhmm  
> Created at: 2024-04-16 10:15:31 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/612#issuecomment-2058737229  

Hi. This doesn't look expected to me. Leading zeros hardly should change base of parsing. But since it is C standard compliant, lets close this ticket.
