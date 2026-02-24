# #11 - [peer-review] Add a friendlier getting-started section and introduction to Bloom filters [Closed]

> Username: Becheler  
> Created at: 2025-05-31 10:07:28 UTC  
> Updated at: 2025-06-19 09:27:49 UTC  
> Closed at: 2025-06-18 20:13:04 UTC  
> Url: https://github.com/boostorg/bloom/issues/11  

# Suggestion(s) for a Beginner-Friendly Introduction to Bloom Filters  
  
_Extracted from and built upon Peter Turcan’s feedback (May 17 2025):_  
  
> I think the doc could use a much friendlier introduction that explains the purpose of these useful filters to all C++ developers. I suggest something like this:  
  
## Why Bloom Filters?  
  
### Motivation  
- Explain the challenge of membership checks against large datasets (e.g., checking a username, URL, or genome sequence).  
- Emphasize the need for speed and space efficiency (e.g., avoiding expensive full-text searches on millions of entries).  
  
### Key Property  
- **No false negatives** (100% certainty if “not present”).  
- **Possible false positives** (probabilistic “might be present”).  
  
### Real-World Example (High-Level)  
- **Fraud detection**: check “bad actors” email addresses before granting access.  
- **Genome screening**: quickly filter out unlikely DNA sequences.  
- **High-frequency trading**: rapidly detect duplicate or recent orders.  
  
---  
  
## High-Level Overview: How Bloom Filters Work  
  
### Core Idea  
- A fixed-size bit array + multiple hash functions.    
- On insertion, each hash sets one or more bits.    
- On lookup, each hash checks those bits;    
  - if any bit is zero → **“definitely not present”**    
  - if all are one → **“possibly present.”**  
  
### Simple Analogy (ASCII Diagram)  
  
```  
Bit‐array: [ 0 0 0 0 0 0 0 0 ]  
Hash functions: h1(item), h2(item), …  
  
Insert “foo”:  
h1("foo") → index 2 → set bit 2 → [ 0 0 1 0 0 0 0 0 ]  
h2("foo") → index 5 → set bit 5 → [ 0 0 1 0 0 1 0 0 ]  
  
Lookup “bar”:  
h1("bar") → index 3 → check bit 3 → 0 → “definitely not present”  
  
Lookup “baz”:  
h1("baz") → index 2 → check bit 2 → 1  
h2("baz") → index 5 → check bit 5 → 1  
→ “possibly present” → needs full-text check to confirm  
```  
  
---  
  
## Concrete “Bad Actors” Example  
  
### Simplified Filter Logic (Illustrative, Not Real)  
- **Rule**: “If (email contains ‘e’) → positive; else negative.”  
  
- **Tony-Montana@mail.com**    
  - no ‘e’ → negative → grant access.  
  
- **John-Dillinger@mail.com**    
  - contains ‘e’ → positive → do full-text lookup.  
  
#### Takeaway  
- Eliminates many expensive database searches by quickly ruling out “definitely not present” cases.  
- In real implementations, multiple independent hash functions replace the single “contains ‘e’” rule.  
  
---  
  
## Terminology & Core Concepts  
  
### Bit Array / Filter Size (“bit_capacity”)  
- Defines how many bits underlying the filter has.  
- Larger arrays → lower false-positive rate (FPR) but more memory.  
  
### Hash Functions (k)  
- Number of independent hash functions applied per element.  
- More hashes → lower FPR up to an optimum, but slower insert/lookup.  
  
### False-Positive Rate (FPR)  
- Probability that “filter says present” for an element that is actually absent.  
- Tunable based on bit array size and number of hash functions.  
  
### Optimal Parameter Selection (Brief Mention)  
- “X‐axis = (bit_capacity) / (expected_element_count)” helps choose k and size.  
- Refer readers to the Bloom Filter Primer for details.  
  
---  
  
## Why Boost.Bloom? (C++-Focused Benefits)  
  
### Intuitive C++ Interfaces  
- Familiar template usage, predictable member functions (e.g., `insert`, `may_contain`).  
- No surprises: follows Boost naming conventions.  
  
### Performance & Optimizations  
- SIMD‐accelerated operations for bulk inserts/lookups.  
- Cache‐friendly blocked design for real‐world speed.  
  
### Ease of Integration  
- Header-only usage via `<boost/bloom.hpp>`.  
- CMake support:    
  ```cmake  
  find_package(Boost REQUIRED COMPONENTS bloom)  
  
---  
  
## Extensive Documentation  
- Filter Primer, examples, benchmarks, and reference sections available for deeper dives.  
  
---  
  
## Getting Started: Quick “Hello World”  
  
### Prerequisites  
- **C++17** (or newer) compiler: GCC 10+, Clang 11+, MSVC 2019+ (minimum).  
- **CMake 3.15+** (for building examples).  
  
### Installation Snippet (CMake)  
```cmake  
find_package(Boost REQUIRED COMPONENTS bloom)  
add_executable(my_app main.cpp)  
target_link_libraries(my_app PRIVATE Boost::bloom)  
  
### Basic code example  
  
```cpp  
#include <boost/bloom.hpp>  
#include <iostream>  
#include <string>  
  
int main() {  
    // 1. Create a filter expecting ~1'000 elements with 0.01 FPR  
    boost::bloom::filter filter(1000, 0.01);  
  
    // 2. Insert some items  
    filter.insert("alice@example.com");  
    filter.insert("bob@example.com");  
    filter.insert("eve@example.com");  
  
    // 3. Check membership  
    std::string name = "mallory@example.com";  
    if (filter.may_contain(name)) {  
        std::cout << name << " is possibly in the set; perform full check.\n";  
    } else {  
        std::cout << name << " is definitely not in the set.\n";  
    }  
  
    return 0;  
}  
```  
  
### Expected Output  
  
```  
mallory@example.com is definitely not in the set.  
```

---

## Comment 1

> Username: joaquintides  
> Created at: 2025-06-14 08:53:04 UTC  
> Url: https://github.com/boostorg/bloom/issues/11#issuecomment-2972496141  

Question to you @Becheler: how do you find the [primer](https://28.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html#primer) inadequate/improvable? I understand it covers motivation, key property and real example. How these beasts work is also explained, as well as the terminology (FPR, bit array, capacity). Optimal parameter selection is explained [here](https://28.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html#configuration). There's a getting started section and a complete snippet to try right at the beginning of the docs. Peter read a former version of all this, so maybe it's now in a better shape. Relevant modifications:  
  
* [Added Getting Started section](https://github.com/joaquintides/bloom/commit/1488ca12bd4eb9c041d00b96737fecc233dd7040)  
* [Made first code snippet self-contained](https://github.com/joaquintides/bloom/commit/b670f6ec52b4b781dfd5a00a1e57d14159492f0b)

---

## Comment 2

> Username: Becheler  
> Created at: 2025-06-14 10:58:53 UTC  
> Updated at: 2025-06-14 11:21:45 UTC  
> Url: https://github.com/boostorg/bloom/issues/11#issuecomment-2972613146  

I will dump my thoughts here, you don't have to agree of course.  
  
1. Maybe seperate the example from the general case by placing them in a squared box with a clear title (Example1: Database application). This is standard in applied math articles but idk if your editor allows to do this, but there are a bunch of options (quotes, notes, mermaid, custom html)  
  
<img width="1025" alt="Image" src="https://github.com/user-attachments/assets/c4ab1229-03db-446c-a634-3ad51834cba2" />  
  
2. You generally want the generalities to come before singular use case. So it I would rephrase:   
- > In general, Bloom filters are useful to prevent/mitigate queries against large data sets when exact retrieval is costly and/or can’t be made in main memory. Applications have been described   
- into :  
- > `In general, Bloom filters are useful to prevent/mitigate queries against large data sets when exact retrieval is costly and/or can’t be made in main memory` just before you present Example 1, then I would give thev example, then reopen with `Other applications have been described ... `  
  
3.  Illustrations  
- I really appreciate your illustrations!   
- When I first read your documentation a few months ago, I didn’t immediately connect all the dots. In particular, for the first figure, I’d suggest making it explicitly clear that the grey bits are those set to 1, and that there are six of them. While you do mention it, it's currently a bit (pun intended!) subtle.  
- I’d also recommend updating the legend to something like: “Inserting element x into a Bloom filter with k = 6 hash functions sets bits at positions 2, 13, 45, and 75 to 1” and maybe show those indices on the picture.  
- As a general rule, we recommend having self-sufficient figures and boxes, that is the reader should not have to read the surrounding text to be able to understand the figure.  
- For readers who are new to the topic, connecting these elements directly in the first figure is really helpful. Once that foundation is clear, you can afford to scale back on the level of detail in the rest of the document. See for example:  
![Image](https://github.com/user-attachments/assets/88296bbc-4531-443b-bda2-e1bdf8bc1381)  
- Same for Figure 3: first time I saw it, it was not clear what was happening. I suggest simplifying the figure just a bit and rephrasing the legend so it's self-suficient: (i) specify y is the element looked up, (ii) reduce the number of negatives (the red hash functions) from 2 to 1 (i guess the filter would not evaluate the second negative anyway ?), and instead of the red hash square box, place a green check symbol under each validated bit, and a red cross under the invalid bit, and then you can rephrase the legend as: "Checking element y in a Bloom filter (k = 6): bits set by first 4 hashes match, but hash 5 finds a 0 — lookup fails"  
  
4. FPR illustration: I would (i) add n to the x-axis label, (ii) add a k_opt curve (iii) rephrase the legend so it's self-suficient: something along  
> More hash functions help early, but hurt later. False positive rate (FPR) as a function of inserted elements for two Bloom filters (m = 10⁵). With k = 2, FPR grows slowly but starts high; with k = 6, FPR is lower initially but rises faster as the filter saturates. Optimal value is k_opt = m.ln2 / n (green curve) (im not sure about this but i think it's important to give an intuition of what optimal compromise the beginner is after)  
  
I will continue later with other remarks, but one thing is certain: your doc is excellent, and that's a lot of work ! 👏🏽

---

## Comment 3

> Username: joaquintides  
> Created at: 2025-06-14 17:11:29 UTC  
> Updated at: 2025-06-14 17:17:29 UTC  
> Url: https://github.com/boostorg/bloom/issues/11#issuecomment-2972878697  

I'm addressing your comments in a dedicated branch, you can see the results so far at  
  
https://29.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html  
  
Will continue committing to this branch if you have further comments (the link above will show additional commits automatically).

---

## Comment 4

> Username: Becheler  
> Created at: 2025-06-16 12:14:54 UTC  
> Url: https://github.com/boostorg/bloom/issues/11#issuecomment-2976425889  

I like it better, thank you Joaquin for making the changes :) What do you think ?   
  
Still nitpicking, but maybe you can move this up to the paragraph so the figure is the last item of the section and the text is not so split:  
  
<img width="1023" alt="Image" src="https://github.com/user-attachments/assets/e7ba4055-96a1-4925-861d-be6523f3166f" />  
  
Also, FPR and probabilistic behavior may be confusing for people so maybe it would not hurt to add something like:  
  
> Bloom filters don’t store items directly — they record which bits were set by k hash functions during insertion. When testing for membership, the question being asked is not so much "is this item present?", but rather “Could this item have set these bits?” If any of the k bits are 0, the item is definitely not in the set. If all are 1, the item might be present — or the result might be a false positive. In short, a false positive occurs when the bits checked happen to be all set to one due to other, unrelated insertions.

---

## Comment 5

> Username: joaquintides  
> Created at: 2025-06-16 16:42:25 UTC  
> Url: https://github.com/boostorg/bloom/issues/11#issuecomment-2977324512  

> I like it better, thank you Joaquin for making the changes :) What do you think ?  
  
I thikn it looks better :-) but I'm trusting your eyes here as I've gone through it so many times I've developed some kind of color blindness towards it.  
  
  
> Still nitpicking, but maybe you can move this up to the paragraph[...]  
  
Done, recheck https://29.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html#primer_implementation  
  
> Also, FPR and probabilistic behavior may be confusing for people so maybe it would not hurt to add something like:  
  
I find this a little redundant, at the beginning of the primer we already say:  
  
> Inserting an element _x_ reduces to selecting _k_ positions pseudorandomly (with the help of _k_ independent hash functions) and setting them to one. To check if an element _y_ is in the filter, we follow the same procedure and see if the selected bits are all set to one.

---

## Comment 6

> Username: Becheler  
> Created at: 2025-06-16 16:57:06 UTC  
> Updated at: 2025-06-16 16:59:12 UTC  
> Url: https://github.com/boostorg/bloom/issues/11#issuecomment-2977364212  

Ok I trust your take on it !  
  
Meanwhile I came with this to re-explain myself the Subfilter section: I don't know if some part of it could enrich this section. My main worry is that I would have been unable to select a Subfilter policy in the documentation current state, and I thought we may need a bit more guidelines and recommendations, which I tried to give. Please forgive my mistakes if any 😓   
  
## Subfilters  
  
A **Subfilter** in Boost’s Bloom filter implementation defines the local strategy for setting or checking bits within a selected bucket of the bit array. It determines how many bits are modified per operation, how they are arranged in memory, and how memory is accessed.  
  
By selecting an appropriate subfilter, users can tune the trade-off between performance, false positive rate (FPR), and memory access patterns. The choice depends on workload characteristics, accuracy requirements, and target hardware capabilities.  
  
| Subfilter                  | Pros                                           | Cons                                             |  
|----------------------------|------------------------------------------------|--------------------------------------------------|  
| `block<Block, K'>`         | Very fast (single memory access)              | Higher FPR (bits clustered)                      |  
| `multiblock<Block, K'>`    | Lower FPR (bits spread out)                   | Slower (multiple memory accesses)                |  
| `fast_multiblock32<K'>`    | Fast + low FPR with SIMD (x86/ARM)            | Requires SSE2/AVX2/Neon                          |  
| `fast_multiblock64<K'>`    | Fast + low FPR with SIMD (64-bit AVX2)        | Requires AVX2 support                            |  
  
### Subfilter Selection Guidelines  
  
- The default subfilter, `block<unsigned char, 1>`, corresponds to a **classical Bloom filter**, setting `K` bits per element uniformly across the array.    
  This is a practical default for general-purpose use, offering predictable behavior with minimal tuning. Recommended for educational contexts, modest workloads, or when performance and FPR requirements are not strict.  
  
- Select **`block<Block, K'>`** when maximum **throughput** is desired and a slightly higher FPR is acceptable. This configuration is well-suited for cache-sensitive applications or systems with high insertion/query rates.  
  
- Select **`multiblock<Block, K'>`** when a **lower false positive rate** is required and moderate performance overhead is acceptable. This is suitable for scenarios prioritizing accuracy, such as set membership checks with correctness constraints.  
  
- Select **`fast_multiblock32<K'>`** or **`fast_multiblock64<K'>`** to achieve both **high performance** and **lower FPR**, provided the target platform supports SIMD instructions (SSE2, AVX2, or Neon). These variants are recommended for performance-critical applications on modern hardware.  
  
### Tuning with `K′`  
  
Once a subfilter type is selected, its behavior can be further adjusted using the `K′` parameter, which specifies how many bits are set per subfilter invocation.  
  
The total number of bits set per element is given by:  K_total = K × K', where `K` is the number of selected buckets (hashes), and `K′` is the number of bits set within each selected bucket.  
  
To match the classical Bloom filter optimum, aim for: K × K′ ≈ (m / n) × ln(2), where `m` is the total number of bits in the filter and `n` is the expected number of elements.  
  
Start with a small `K′` and increase only if lower FPR is required and performance permits:  
  
| Objective                  | Suggested `K′` |  
|----------------------------|----------------|  
| Fastest performance        | 1 – 2          |  
| Lower false positive rate  | 3 – 6          |  
| Balanced configuration     | 2 – 4          |  
  
For more background on the underlying filter types, see the [Bloom Filter Primer].

---

## Comment 7

> Username: joaquintides  
> Created at: 2025-06-16 17:01:28 UTC  
> Url: https://github.com/boostorg/bloom/issues/11#issuecomment-2977376157  

I like that. Let me take some time to adapt portions of it for use in the tutorial --my tone is somewhat drier than yours :-)

---

## Comment 8

> Username: Becheler  
> Created at: 2025-06-16 17:02:37 UTC  
> Url: https://github.com/boostorg/bloom/issues/11#issuecomment-2977379269  

I notice ahaha ! AND I TRIED TO BE DRIER bahaha xD

---

## Comment 9

> Username: Becheler  
> Created at: 2025-06-16 19:22:45 UTC  
> Updated at: 2025-06-16 19:46:10 UTC  
> Url: https://github.com/boostorg/bloom/issues/11#issuecomment-2977821037  

Meanwhile I took a try for the BucketSize section:  
  
## BucketSize  
  
A **subarray** is a contiguous region of memory (in bytes) that a subfilter operates on during insertions and queries. Its size depends on the subfilter type:  
- For `block<Block, K'>`, the subarray size is the size of `Block` (e.g., 8 bytes for `uint64_t`).  
- For `multiblock<Block, K'>`, the subarray consists of `K'` consecutive `Block` elements.  
  
Subarrays are extracted from the global bit array. The `BucketSize` parameter controls how far apart subarrays are positioned — that is, the stride (in bytes) between consecutive subarrays.  
  
When `BucketSize = 0` (the default), the stride is automatically set to the subarray size. This produces **non-overlapping subarrays**, with each subfilter operating on a distinct, aligned memory region. This layout favors **fast, cache-efficient access**.  
  
When `BucketSize` is set to a smaller value, subarrays begin at closer positions and may **overlap**. For example:  
- `BucketSize = 8` (with 8-byte subarrays) → no overlap    
- `BucketSize = 1` → maximum overlap    
- `BucketSize = 0` → automatically matches subarray size → no overlap  
  
Overlapping subarrays may appear to increase collision risk, but in Bloom filters, the main cause of false positives is **saturation** — too many distinct bits set to `1`, increasing the probability that unrelated queries match.  
  
Overlap introduces **bit reuse**: many of the bits set by an insertion may already be `1`, especially when subarrays partially cover the same regions. As a result, each insertion introduces **fewer new 1s**, which **slows saturation** and leads to a **lower false positive rate**.  
  
> Analogy:  
> Each insertion can be viewed as spending a fixed number of "coins" to flip bits:  
> - With non-overlapping subarrays, each coin flips a different bit.  
> - With overlapping subarrays, many coins land on already-set bits.  
> The more coins are reused, the slower the filter fills, and the longer it maintains its accuracy.  
  
This leads to a core trade-off between accuracy and performance:  
  
- **False Positive Rate (FPR)**    
  - Smaller `BucketSize` → more overlap → slower saturation → **lower FPR**    
  - Larger or default `BucketSize` (no overlap) → more unique bits set → **higher FPR**  
  
- **Performance (Memory Access)**    
  - Larger `BucketSize` (or `0`) → aligned subarrays → **faster access, better cache locality**    
  - Smaller `BucketSize` → overlapping subarrays → possible unaligned access → **slower lookups and inserts**  
  
> Recommendations:  
> - Use **`BucketSize = 0`** (default) for best performance and alignment.  
> - Use a **smaller value** (e.g., `1` or `2`) if **lower FPR** is required and **memory performance** is less critical.

---

## Comment 10

> Username: joaquintides  
> Created at: 2025-06-17 17:00:02 UTC  
> Updated at: 2025-06-17 17:00:19 UTC  
> Url: https://github.com/boostorg/bloom/issues/11#issuecomment-2981129004  

Subfilter section rewritten:  
https://29.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html#tutorial_subfilter  
Do you like it better?

---

## Comment 11

> Username: Becheler  
> Created at: 2025-06-17 20:08:20 UTC  
> Updated at: 2025-06-17 20:10:00 UTC  
> Url: https://github.com/boostorg/bloom/issues/11#issuecomment-2981692264  

Love it ! Thanks for updating and rephrasing 🥳   
One thing I noticed while commuting and reading your message on mobile: the table (and the documentation in general) doesn’t display well. I’m not sure if you’ve ever checked the doc on mobile, or if maybe the large Subfilter table broke the layout.  
  
Have you had any chance to have a look at the updated BucketSize proposal (see  ☝️) ?

---

## Comment 12

> Username: joaquintides  
> Created at: 2025-06-18 07:22:48 UTC  
> Url: https://github.com/boostorg/bloom/issues/11#issuecomment-2982999739  

> One thing I noticed while commuting and reading your message on mobile: the table (and the documentation in general) doesn’t display well. I’m not sure if you’ve ever checked the doc on mobile, or if maybe the large Subfilter table broke the layout.  
  
Shows fine for me in an iPhone. My experience with Asciidoc is that it does a decent job on mobile devices. Care share a screenshot?  
  
> Have you had any chance to have a look at the updated BucketSize proposal (see ☝️) ?  
  
Yes. Inspired by your use of the term "stride", I've realized the introduction of "buckets" in the explanation is not helping readers. So, I've rewritten everything in terms of strides (including the source code):  
  
https://29.bloom.prtest3.cppalliance.org/libs/bloom/doc/html/index.html#tutorial_stride  
  
How do you like it?

---

## Comment 13

> Username: Becheler  
> Created at: 2025-06-18 13:09:27 UTC  
> Url: https://github.com/boostorg/bloom/issues/11#issuecomment-2984150487  

Is that the expected outcome ?  
  
No zoom:  
![image](https://github.com/user-attachments/assets/eb3c2e23-af60-4012-aa5d-4ebc8ae83796)  
  
Zooming out:  
![image](https://github.com/user-attachments/assets/a1ec8355-7934-4299-9e00-4643d98d082f)

---

## Comment 14

> Username: Becheler  
> Created at: 2025-06-18 13:12:38 UTC  
> Url: https://github.com/boostorg/bloom/issues/11#issuecomment-2984161678  

As for the Stride—excellent! I didn’t dare suggest renaming BucketSize, knowing it would affect the source code, but I’m glad you went ahead with it! I’m satisfied with the current state, though I’m still a bit unsure whether people will know how to use it. I suppose they’d need to dig into Bloom Filter literature, which the documentation is't really supposed to be a placeholder for :)

---

## Comment 15

> Username: joaquintides  
> Created at: 2025-06-18 14:18:55 UTC  
> Url: https://github.com/boostorg/bloom/issues/11#issuecomment-2984413689  

> Is that the expected outcome ?  
  
This is what I see on my device, yes. The table is too wide to fit and you've got to scroll laterally, but I don't know what other option would be better than this --nor would I know how to get it with Asciidoc.

---

## Comment 16

> Username: Becheler  
> Created at: 2025-06-18 14:29:29 UTC  
> Url: https://github.com/boostorg/bloom/issues/11#issuecomment-2984454869  

Im used to have tables too wide to fit in page being displayed in horizontally scrollable boxes, I think markdown on GitHub does that.  
  
Apparently you can try to do something similar with asciidoc, the advantage is that it does not break formatting on overflow:   
  
```  
++++  
<div style="overflow-x: auto;">  
++++  
  
[cols="1,1,1,1", options="header"]  
|===  
| Column 1 | Column 2 | Column 3 | Column 4  
  
| Value A1 | Value A2 | Value A3 | Value A4  
| Value B1 | Value B2 | Value B3 | Value B4  
|===  
  
++++  
</div>  
++++  
```

---

## Comment 17

> Username: joaquintides  
> Created at: 2025-06-18 15:29:43 UTC  
> Url: https://github.com/boostorg/bloom/issues/11#issuecomment-2984699007  

You certainly know more HTML than me. Added, care recheck?

---

## Comment 18

> Username: Becheler  
> Created at: 2025-06-18 15:37:37 UTC  
> Url: https://github.com/boostorg/bloom/issues/11#issuecomment-2984729902  

Ahaha I've had my fair share of math/pics/table overflow in my genetics library documentation:)   
  
It looks better but one huge math formula is still ruining the display and could probably benefit the same treatment:  
  
![image](https://github.com/user-attachments/assets/8c7e5c0f-a778-4374-91fb-11c27ffd1be3)

---

## Comment 19

> Username: joaquintides  
> Created at: 2025-06-18 16:08:18 UTC  
> Url: https://github.com/boostorg/bloom/issues/11#issuecomment-2984864885  

Done! Please recheck

---

## Comment 20

> Username: Becheler  
> Created at: 2025-06-18 17:01:02 UTC  
> Updated at: 2025-06-18 17:03:17 UTC  
> Url: https://github.com/boostorg/bloom/issues/11#issuecomment-2985052834  

That's too perfect 🥹🥲  
I will skip through the remaining of the doc and unless there are more issues I guess I'll probably close this :)

---

## Comment 21

> Username: Becheler  
> Created at: 2025-06-18 20:13:00 UTC  
> Url: https://github.com/boostorg/bloom/issues/11#issuecomment-2985564059  

Looks good to me 🥳    
Bravo on this nice looking documentation !   
Minor remarks that don't warrant further changes :   
- I found myself a bit lost while scrolling in the headers/class templates items, maybe adding a bunch of horizontal lines to divide categories a bit more could help, idk.   
- "Without formal justification" sounds a bit dismissive. Maybe rephrase your unknown saying that while the heuristic does not respect theoretical assumptions of ... (i guess some independence of hash values) ... it has shown to offer a good balance between performance and false positive rate for typical workloads.  
- In the hash category, I was kinda surprised the lib would modify/override the user-provided hash function. It feels like a loss of control. I am surely misunderstanding what you do and why you do it (I'm no RNG guy) but I would have appreciated one or two sentences of context around this 😄

---

## Comment 22

> Username: joaquintides  
> Created at: 2025-06-19 07:29:18 UTC  
> Updated at: 2025-06-19 09:27:49 UTC  
> Url: https://github.com/boostorg/bloom/issues/11#issuecomment-2987043327  

> I found myself a bit lost while scrolling in the headers/class templates items, maybe adding a bunch of horizontal lines to divide categories a bit more could help, idk.  
  
Added thematic breaks in the reference, hope it looks clearer now.  
  
> "Without formal justification" sounds a bit dismissive. Maybe rephrase your unknown saying that while the heuristic does not respect theoretical assumptions of ... (i guess some independence of hash values) ... it has shown to offer a good balance between performance and false positive rate for typical workloads.  
  
It's not a performance/FPR tradeoff... The resulting FPR is indistiguishable from the theoretical one with $$k$$ independent hash functions. The required formal justification would be a mathematical proof that the values $$\textbraceleft h_i \textbraceright$$ obtained from $$h_0$$ are probabilistically as well behaved as independent random variables. Some authors have proved similar things for other hash-reduction mechanisms, but this is definitely beyond my mathematical abilities.  
  
> In the hash category, I was kinda surprised the lib would modify/override the user-provided hash function. It feels like a loss of control. I am surely misunderstanding what you do and why you do it (I'm no RNG guy) but I would have appreciated one or two sentences of context around this   
  
Technically, it's post-processing the results of the hash function so as to make them more sound from a statistical point of view. So, we're not overriding it. The post-mixing can be opted out by the user if needed (marking the function as `is_avalanching`). This is the same procedure adopted by Boost.Unordered with good results. The rationale is that some hash functions (for instance, `std::hash` or `boost::hash` for integral types) are tremendously bad (like, the identity function), and they won't perform ok as is.
