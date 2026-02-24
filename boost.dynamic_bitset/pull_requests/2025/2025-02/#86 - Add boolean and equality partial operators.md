# #86 Add boolean and equality partial operators [Closed]

> Username: joprodrigues  
> Created at: 2025-02-11 20:32:58 UTC  
> Updated at: 2025-08-02 06:19:38 UTC  
> Closed at: 2025-08-02 06:19:38 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/86  

Create boolean (or, and, xor and sub) and equality operators, that operate on two distinct bitsets with different starting points, but same length. Use the partial view as the base object for the partial operations.

---

## Comment 1

> Username: joprodrigues  
> Created_at: 2025-02-11 20:36:35 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/86#issuecomment-2652012688  

Follow up of #53 , because I could not change the origin branch

---

## Comment 2

> Username: joprodrigues  
> Created_at: 2025-02-16 18:07:00 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/86#issuecomment-2661551504  

I think the failure was a spurious one, and not indicative of any problems in the MR itself

---

## Comment 3

> Username: gennaroprota  
> Created_at: 2025-08-01 14:32:46 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/86#issuecomment-3144796677  

I'm sorry to say this, as it's clear you've put significant effort into it, but this feels like too much complexity for a questionable benefit.

---

## Comment 4

> Username: joprodrigues  
> Created_at: 2025-08-01 17:11:12 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/86#issuecomment-3145231133  

Should I close the Pull Request?  
I don't see any way of making this simpler.

---

## Comment 5

> Username: gennaroprota  
> Created_at: 2025-08-01 17:46:04 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/86#issuecomment-3145321549  

Yes, I feel it’s best to close this. But don’t get discouraged—there'll be another opportunity. Really appreciate the effort you put into it.

---
