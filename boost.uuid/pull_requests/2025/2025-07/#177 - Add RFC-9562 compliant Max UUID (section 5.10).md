# #177 Add RFC-9562 compliant Max UUID (section 5.10) [Merged]

> Username: jeking3  
> Created at: 2025-07-03 20:53:09 UTC  
> Updated at: 2025-09-09 10:30:46 UTC  
> Merged at: 2025-08-09 18:28:05 UTC  
> Closed at: 2025-08-09 18:28:05 UTC  
> Url: https://github.com/boostorg/uuid/pull/177  

Section 5.10 of https://datatracker.ietf.org/doc/rfc9562/ defines a "max" uuid which is not present in boost::uuid.  
  
The clang-win build issue is a known issue: https://github.com/actions/runner-images/issues/12435

---

## Comment 1

> Username: pdimov  
> Created_at: 2025-08-09 20:37:33 UTC  
> Url: https://github.com/boostorg/uuid/pull/177#issuecomment-3172083235  

Please don't merge pull requests without my approval.

---

## Comment 2

> Username: pdimov  
> Created_at: 2025-08-16 17:24:46 UTC  
> Url: https://github.com/boostorg/uuid/pull/177#issuecomment-3193800905  

I'm not opposed to adding the "max" UUID from the spec; but I'm not sure we want to add a new generator for each constant. We don't have generators for each namespace constant, for instance.  
  
I suppose there was once the idea that the generators could be used in generic code templated by Generator, but even if this proved useful to anyone - which I doubt - we still have no firm grounds for adding generators for each constant.  
  
I think that a better course of action would be to either add the `max_uuid()` function to the main header, or perhaps add a separate `boost/uuid/constants.hpp` header and put it there.

---

## Comment 3

> Username: pdimov  
> Created_at: 2025-09-09 10:30:46 UTC  
> Url: https://github.com/boostorg/uuid/pull/177#issuecomment-3270040873  

I've added `boost/uuid/constants.hpp`.  
  
@jeking3 would you please consider making a new PR that adds the max UUID there so that authorship is preserved?

---
