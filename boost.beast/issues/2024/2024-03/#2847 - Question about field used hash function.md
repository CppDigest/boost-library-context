# #2847 - Question about field used hash function [Closed]

> Username: emmett2020  
> Created at: 2024-03-31 12:32:00 UTC  
> Updated at: 2024-04-02 02:37:17 UTC  
> Closed at: 2024-04-02 02:37:17 UTC  
> Url: https://github.com/boostorg/beast/issues/2847  

Hi, I'm a little wondering the hash function name which used by beast::http::detail::field_table. Seems like it's a multiplying hash function. But I don't exactly know why we should multiple 5 other than other prime values in field_table::digest(). And why the size of hash table is 5155? I have tried to search the relevant materials, but can not find the relevant explanation. Could you help solve the confusion? Thank you.  
<img width="593" alt="image" src="https://github.com/boostorg/beast/assets/59395781/b858b4e9-0de2-4547-bdf9-ac0d8ad6940e">  
<img width="426" alt="image" src="https://github.com/boostorg/beast/assets/59395781/8f8fffc5-11e6-4387-bb6c-7961e19cc110">

---

## Comment 1

> Username: ashtum  
> Created at: 2024-03-31 13:04:59 UTC  
> Updated at: 2024-03-31 13:16:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2847#issuecomment-2028717403  

I believe 5155 is chosen because the table only has two buckets for collision resolution, and that size accommodates everything fitting into the table with at most one collision.  
This is the relevant commit: https://github.com/boostorg/beast/commit/e0db595760ece2f412320b0336e9f57399e4cd4a.  
@pdimov, Could you please shed some light on the reasoning behind these?

---

## Comment 2

> Username: emmett2020  
> Created at: 2024-03-31 13:25:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2847#issuecomment-2028732335  

> I believe 5155 is chosen because the table only has two buckets for collision resolution, and that size accommodates everything fitting into the table with at most one collision. @pdimov, Could you please shed some light on the reasoning behind these?  
  
Thanks for such a quick answer:) table does have only two buckets to handle conflicts, and, uh, now I'm getting more curious about how 5155 works better than the other primes in the case of only two buckets.

---

## Comment 3

> Username: pdimov  
> Created at: 2024-03-31 14:06:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2847#issuecomment-2028759599  

If I remember correctly, 5155 has been chosen empirically, by trying all sizes until one has the right properties (no collisions in the first 256 names and no collisions in the last n-256 names.)  
  
This should probably be converted into a proper perfect hash table one day as this one is a bit ad hoc. Maybe @joaquintides can find this an interesting application of his latest research.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2024-03-31 14:13:10 UTC  
> Url: https://github.com/boostorg/beast/issues/2847#issuecomment-2028762130  

We might also start removing the least used field constants until we have a set of fields for which a highly performant hash function is discovered.

---

## Comment 5

> Username: pdimov  
> Created at: 2024-03-31 14:19:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2847#issuecomment-2028765881  

As for the 5 multiplier, I also don't remember why I chose it; I was probably optimizing lookup time on a synthetic benchmark.  
  
For the multiplicative hash function (`H = m*H + c`), the traditional multipliers are 5, 31 and 33, because (in the past) they would have been efficiently expressed as `(x << 2) + x`, `(x << 5) - x` and `(x << 5) + x`, respectively. (They aren't the best multipliers by any measure; they were just popular in the past.)

---

## Comment 6

> Username: emmett2020  
> Created at: 2024-04-01 03:03:28 UTC  
> Url: https://github.com/boostorg/beast/issues/2847#issuecomment-2029077221  

> As for the 5 multiplier, I also don't remember why I chose it; I was probably optimizing lookup time on a synthetic benchmark.  
>   
> For the multiplicative hash function (`H = m*H + c`), the traditional multipliers are 5, 31 and 33, because (in the past) they would have been efficiently expressed as `(x << 2) + x`, `(x << 5) - x` and `(x << 5) + x`, respectively. (They aren't the best multipliers by any measure; they were just popular in the past.)  
  
Great. I get it. 5 was chosen as a factor for the multiplicative hash function because the multiplication of 5 can be converted into an efficient bit operation. 5155 as the hash table length is derived from the experience value.   
Thank you and others for your answers.
