# #13 - [peer-review] Clarify terms such as capacity vs. bit_capacity, may_contain, and fpr_for [Closed]

> Username: Becheler  
> Created at: 2025-05-31 10:35:12 UTC  
> Updated at: 2025-06-13 18:29:52 UTC  
> Closed at: 2025-06-13 18:29:52 UTC  
> Url: https://github.com/boostorg/bloom/issues/13  

# Description  
  
This issue proposes reviewing and clarifying several terms/names used in the Boost.Bloom documentation and interface. Below are direct quotes from Andrzej Krzemienski’s feedback (May 18th) , highlighting the terms that may need clearer naming or explanation.   
  
For each term listed, please consider whether:  
1. The current name should be renamed or accompanied by additional explanation.  
2. If unchanged, provide a rationale so users understand the naming rationale.  
  
---  
  
## 1. `capacity` → `bit_capacity`  
  
- > Use the term "bit_capacity" rather than "capacity" for representing capacity in bits. This is not to accidentally mislead the users.  
- **Issue:** Users may assume `capacity` refers to the number of elements rather than the number of bits.  
- **Action Needed:** Rename to `bit_capacity` or explicitly note in documentation that `capacity` means "number of bits."  
  
---  
  
## 2. `may_contain` vs. `contains`  
  
- > The user is asking, "do you contain the value", and the library is replying, "maybe I do, and maybe I don't"  
- **Issue:** `may_contain` implies probabilistic semantics (“maybe present”), but is awkward English.  
- **Action Needed:** Decide whether to keep `may_contain` and add a note explaining its semantics, or explore alternative names (e.g., `possibly_contains`, `possibly_present`).  
  
---  
  
## 3. Operator `|=` (“set union”)  
- > When describing the |= operator, use the name "set union", which is the industry standard. The current choice of words "containing both the elements of f and f2" can be interpreted both as a union and as an intersection.  
- **Issue:** The phrase “containing both the elements of f and f2” is ambiguous.  
- **Action Needed:** In documentation, explicitly call `filter1 |= filter2` a **“set union”** operation on bit patterns to avoid confusion.  
  
---  
  
## 4. Operator `&=` (Intersection Semantics)  
- >  I am uneasy about the functionality under &= operator. It implies symmetry with operator |=. But the symmetry is broken for at least two reasons: i) If I put some elements to filter f1 and some other elements to filter f2, and then I apply |=, then the effect is the same as if I put all the elements in f1 to begin with. The same property, if I understand correctly, does not apply to operator &=.  ii) The compromising of the FPR value that you are describing.    
- **Issue:** `&=` suggests a symmetric “intersection” operation but does not behave like a true set intersection in all cases and alters false-positive rates unpredictably.  
- **Action Needed:** Consider replacing `filter1 &= filter2` with a named function (e.g., `intersect_with`) and document how it differs from taking the union.  
  
---  
  
## 5. Suffix `"_for"` in `fpr_for`  
- >Does the suffix "_for" in the name "fpr_for" bring any value?  
- **Issue:** It’s unclear whether `_for` clarifies “false-positive rate for N items” or is redundant.  
- **Action Needed:** Evaluate if `fpr_for` should be renamed to `false_positive_rate`, `compute_fpr`, or similar.  
  
---  
  
## 6. Overloaded `reset()` vs. Separate `reset_for`  
- > Not sure if it is a good idea to have two overloads for `reset()` doing slightly different things. For constructors this is unavoidable, but for member functions, you can have `reset` and `reset_for`.  
- **Issue:** Two `reset()` overloads perform different actions, which can confuse users.  
- **Action Needed:** Rename one overload (e.g., `reset_for(...)`) to make their behaviors explicit.  
  
---  
  
## 7. Preconditions for `|=` and `&=`  
- > I think that you are missing a precondition for operators &= and |=. They work under the assumptions that for both filters f1 and f2 inserting   
  >the same element renders the same bit pattern in the hash. But this is not the case when the hashers that I provide to f1 and f2 have the same type but different initial state  
- **Issue:** The documentation does not state that `|=` and `&=` require filters built with identical hasher states to produce consistent bit patterns.  
- **Action Needed:** Document the precondition: “Filters must use hashers that produce identical bit patterns for the same element, or results of `|=`/`&=` are undefined.”  
  
---  
  
## Requested Actions  
  
- Review each term above and decide whether to:  
  1. Rename the term/function/operator.  
  2. Add explanatory notes in the documentation (e.g., rationale for `may_contain`, meaning of `bit_capacity`, etc.).  
  3. Leave unchanged (but provide a brief rationale or explanation when closing the issue).  
  
- If any renaming breaks backward compatibility, consider adding an alias or deprecation notice.  
  
- If this issue is closed without adopting any changes, please provide a short explanation for each term explaining why no change was necessary.

---

## Comment 1

> Username: joaquintides  
> Created at: 2025-06-13 17:18:21 UTC  
> Url: https://github.com/boostorg/bloom/issues/13#issuecomment-2971010190  

* `capacity`: In the review I commented I'm happy to rename to `bit_capacity` if more support was expressed, but nobody said anything. I guess we can poll the community again?  
* `may_contain`: I remember some reviewers commenting they were ok with this name. Other Bloom filter libraries use `mightContain`, `probably_contains`, `check`, `contains`, `Find`, etc., so it's not like there's some established terminology. IMHO `may_contain` is as good as `mightContain` and `probably_contains`, and better than `check`, `contains` and `Find`.  
* `operator |=`: I interpreted the reviewer's comment as suggesting that "set union" be used in the _description_ of the operation, not for the name of the member function itself. To that effect, I commited [used "set union" for more clarity](https://github.com/joaquintides/bloom/commit/a276236c4d542646688d93e487744921f5bc43b0).  
* `operator&=`: I understand the reviewer's concern about the disymmetry wrt FPR, but I do't think this justifies changing the name of the member function when `&=` so clearly indicates what's going on. The problem with the FPR is explicitly warned about [here](https://28.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html#tutorial_filter_combination).  
* Suffix in `fpr_for`: The suffix `_for` is meant as an indication that the FPR returned is _for_ the arguments passed, and not the FPR of the particular filter object (this is clear once the user realizes that `fpr_for` is a static member function, but this may go unnoticed). I admit this may not be entirely clear, but I didn't come up with anything better. I don't think that `false_positive_rate` or `compute_fpr` add much more value here.  
* Overloaded `reset`: I don't agree with the reviewer's remark that the different overloads do different things; `reset(n, fpr)` is simply `reset(capacity_for(n, fpr))`.  In favor of my decision, please note that similar uses of `reset` in the standard library (for instance, [`std::unique_ptr::reset`](https://en.cppreference.com/w/cpp/memory/unique_ptr/reset.html)) tend to overload so as to mimic the available constructors (reseting is akin to assigning a newly constructed object): I'm merely following that convention.  
* Preconditions for `|=` and `&=`: [Made hasher equivalence a precondition for &=/|=](https://github.com/joaquintides/bloom/commit/d87848086eed42cbb5bed2ccbbb489c4ea88dfac).

---

## Comment 2

> Username: Becheler  
> Created at: 2025-06-13 18:29:52 UTC  
> Url: https://github.com/boostorg/bloom/issues/13#issuecomment-2971225784  

Thank you for your thorough response.  
You obviously carefully considered all/most possible alternatives and I agree you took reasonable decisions.   
  
I don’t think it’s worth polling the community again on this issue: if the problem is real, it will manifest itself again with users complains.   
  
I will close this issue that can be reopen/redirected to  when these future discussions arise.
