# #412 Fix geometry bug in is_straight_line_drawing [Merged]

> Username: jeremy-murphy  
> Created at: 2024-12-27 22:56:38 UTC  
> Updated at: 2025-01-26 21:22:46 UTC  
> Merged at: 2025-01-26 21:22:46 UTC  
> Closed at: 2025-01-26 21:22:46 UTC  
> Url: https://github.com/boostorg/graph/pull/412  

Fix the geometry bug uncovered and documented by @Hermann-SW by replacing the custom function with one from Boost.Geometry.  
`crosses` meets the requirement described in the old code for returning true for 'intersections' that don't include endpoints or self-intersection.  
I decided to remove the tolerance altogether on the assumption that it was indicative of a poor implementation.

---

## Comment 1

> Username: pdimov  
> Created_at: 2024-12-30 01:13:05 UTC  
> Url: https://github.com/boostorg/graph/pull/412#issuecomment-2564930871  

Why is this assigned to me?

---

## Comment 2

> Username: jeremy-murphy  
> Created_at: 2024-12-30 02:59:24 UTC  
> Url: https://github.com/boostorg/graph/pull/412#issuecomment-2564975862  

> Why is this assigned to me?  
  
Sorry I meant to leave a comment that I just wanted you to give it a sanity check. I've never used the numeric_cast before, but I presume it's as simple as this.

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2024-12-30 03:37:27 UTC  
> Url: https://github.com/boostorg/graph/pull/412#issuecomment-2564992199  

I just don't really know who else to assign my own pull requests to, since I assume JZ Maddock wants to minimise his Boost workload these days.

---

## Comment 4

> Username: jeremy-murphy  
> Created_at: 2025-01-01 22:37:11 UTC  
> Url: https://github.com/boostorg/graph/pull/412#issuecomment-2567168173  

Whoops, accidentally pushed my CMake changes here and force-pushed to get rid of them.

---

## Review 5 [Approved]

> Username: Becheler  
> Created_at: 2025-01-26 20:08:10 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/graph/pull/412#pullrequestreview-2574387045  

The implementation aligns well with the general style and structure of the Boost Graph Library.  
  
The introduction of a new dependency on Boost::Geometry was briefly discussed in issue #388 and deemed worthwhile during that discussion.  
  
The function is well-organized but remains somewhat lengthy despite the effective code reduction by @jeremy-murphy. While I recognize that adding further function extraction, comments, or documentation could help new contributors better understand the context and usage, I understand that this is not the primary focus of this quick fix.  
  
The code appears correct, and all tests pass; however, I am not deeply familiar with planar graph theory. It might be beneficial for someone with more expertise in this area to provide additional input, though @jeremy-murphy’s expertise may already suffice.  
  
In summary, the tests pass, and the code is consistent with the library's conventions. While I may not be fully qualified to evaluate the deeper theoretical aspects, I’m happy to assist further if needed!

---
