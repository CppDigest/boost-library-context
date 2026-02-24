# #793 Proposal for solving fraction_part causing 1.09e-6 generated as 1.9e-6 issue  #792 [Merged]

> Username: sigbjorn  
> Created at: 2024-09-19 14:58:26 UTC  
> Updated at: 2025-05-09 07:43:42 UTC  
> Merged at: 2025-05-09 07:43:42 UTC  
> Closed at: 2025-05-09 07:43:42 UTC  
> Url: https://github.com/boostorg/spirit/pull/793  

Solves issue,   
  
based on using int based compute of number of digits in a positive real-concept type,  
as provided in Shyft workaround by   
 Paul Christoph Bätzing-Rosenvinge: https://gitlab.com/paul.christoph.batzing-rosenvinge  
   
Ref to #792

---

## Comment 1

> Username: djowel  
> Created_at: 2024-09-20 02:15:23 UTC  
> Url: https://github.com/boostorg/spirit/pull/793#issuecomment-2362571798  

@hkaiser Tell me what you think?

---

## Comment 2

> Username: hkaiser  
> Created_at: 2024-09-20 13:01:07 UTC  
> Url: https://github.com/boostorg/spirit/pull/793#issuecomment-2363685958  

> @hkaiser Tell me what you think?  
  
I'm fine with this if all the tests pass.

---

## Comment 3

> Username: saki7  
> Created_at: 2025-05-09 07:40:55 UTC  
> Updated_at: 2025-05-09 07:42:49 UTC  
> Url: https://github.com/boostorg/spirit/pull/793#issuecomment-2865493941  

I'm not entirely convinced this is the best approach. There's likely a better way than multiplying by 10 in a loop to analyze decimal digits. That said, a more optimized implementation would require a deep understanding of IEEE floating-point representation. For now, I think this is an acceptable solution, as it addresses a real issue effectively.  
  
Thanks for the PR!

---
