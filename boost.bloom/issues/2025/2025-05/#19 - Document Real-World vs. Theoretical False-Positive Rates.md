# #19 - [peer-review]  Document Real-World vs. Theoretical False-Positive Rates [Closed]

> Username: Becheler  
> Created at: 2025-05-31 17:17:13 UTC  
> Updated at: 2025-06-16 10:24:03 UTC  
> Closed at: 2025-06-13 16:47:08 UTC  
> Url: https://github.com/boostorg/bloom/issues/19  

## Description  
  
Kostas Savvidis (May 18) observed that the library’s built-in FPR prediction (based on the number of inserted elements) can be overly pessimistic when many duplicates exist. His experiments on genomic data revealed a large gap between “predicted” FPR and the “actual” FPR computed from bit-array occupancy. Below are key excerpts and suggested improvements.  
  
---  
  
### Relevant Quotes  
  
- > “This FPR appears to be overpessimistic, as experiments with the real data showed.”    
- > “When I request fpr=0.01 or 1%, the number of false collisions is only 86k, so actual FPR is probably closer to 0.14%.”    
- > “...`fpr_for` is far from reality: the bit array for FPR=0.9 is 2²⁸ bits wide, of which only 120M are occupied, thus the correct FPR rate is 120/268 ≈ 44%...”    
  
---  
  
## Action Items  
  
1. **Add “Actual FPR” Calculation**    
   - In addition to the apriori formula (`fpr_for(n, capacity)`), compute FPR using the actual bit-array Hamming weight:    
```math       
\text{FPR}_{\text{actual}} = \left( \frac{\text{bits\_set}}{\text{total\_bits}} \right)^k  
```  
   - Track “bits_set” incrementally during insertion (increment only when a new bit flips 0→1) or compute it via a full scan after construction.  
  
2. **Document Both FPR Methods**    
   - In the “Bloom Filter Primer” or “Choosing Parameters” section, explain:    
     - **Theoretical FPR** (uses total insertions `n` assumed unique).    
     - **Real-World FPR** (uses actual number of set bits).    
   - Provide guidance on when each method is appropriate (e.g., highly distinct datasets vs. data with many duplicates).  
  
3. **Provide a Code Example**    
   - Show how to retrieve the bit-array size and Hamming weight, then compute real-world FPR. For example:  
     ```cpp  
     size_t total_bits = filter.bit_capacity();  
     size_t bits_set = filter.hamming_weight();  
     double actual_fpr = std::pow(double(bits_set) / total_bits, k);  
     std::cout << "Actual FPR: " << actual_fpr << "\n";  
     ```  
   - Ensure `hamming_weight()` (or equivalent) is available or documented.  
  
4. **Benchmark & Compare**    
   - Demonstrate, using a small example, the divergence between theoretical and actual FPR when duplicates are present.    
   - Include a table or chart in documentation showing “predicted vs. observed FPR” for a sample dataset.  
  
---  
  
By adding a real-world FPR computation and clear documentation, users can better tune their filters—especially when data contains many repeated items—rather than relying solely on the apriori estimate.    
  
If my understanding of the issues is wrong, please feel free to correct it.

---

## Comment 1

> Username: joaquintides  
> Created at: 2025-06-13 11:07:35 UTC  
> Updated at: 2025-06-16 10:24:03 UTC  
> Url: https://github.com/boostorg/bloom/issues/19#issuecomment-2969992950  

I think this stems from a misunderstanding by the OP about what `fpr_for` returns. Let's stick to the case of a classical Bloom filter for simplicity --the whole discussion extends naturally to other types of filters as provided by Boost.Bloom.  
  
The FPR for a Bloom filter is extensively quoted in the literature as  
  
$$\text{FPR}(n,m,k)=\left(1 - \left(1 - \displaystyle\frac{1}{m}\right)^{kn}\right)^k \approx \left(1 - e^{-kn/m}\right)^k,$$  
  
where $$n$$ is the number of elements inserted, $$m$$ the array capacity in bits and $$k$$ the number of bits set per insertion. Now, $$\text{FPR}(n,m,k)$$ is the expected false positive rate for lookups _after_ insertion of $$n$$ elements, _not during_ the insertion process. Kostas has focused in the (for want of a better name) _running_ FPR as the elements are being inserted. Let us model that: suppose we're inserting $$n$$ distinct elements plus $$d$$ duplicated elements. If we calculate $$d^{\ast}$$ programmatically as follows:  
  
```cpp  
std::size_t d_star = 0;  
for(auto x: ...) {  
  if(f.may_contain(x)) {  
    ++d_star;  
  }  
  else {  
    f.insert(x);  
  }    
}  
```  
  
Then, $$d^{\ast}$$ will not be the same as $$d$$, but instead we have  
  
$$\frac{d^{\ast}-d}{n} \approx \frac{1}{n}\sum_{i=0}^{n} \text{FPR}(i, m, k) \approx \frac{1}{n}\int_{0}^{n} \left(1 - e^{-kx/m}\right)^k dx =$$  
$$= \frac{1}{n}\frac{m e^{-k^2x/m} \left( e^{kx/m} -1 \right)^{k+1} {}_2F_1\left( 1,1;1-k;e^{kx/m} \right)}{k^2}\bigg|_0^n$$  
  
(integration done with [WolframAlpha](https://www.wolframalpha.com/input?i2d=true&i=Integra%5C%2891%29Power%5B%5C%2840%291-exp%5C%2840%29-Divide%5Bkx%2Cm%5D%5C%2841%29%5C%2841%29%2Ck%5D%5C%2844%29x%5C%2893%29&lang=en), $${}_2F_1$$ is the [hypergeometric function](https://en.wikipedia.org/wiki/Hypergeometric_function)). This expression is what Kostas is after and we have called running FPR. I think the misundersanding is to assume that $$\(d^{\ast}-d)/n$$ is an approximation of $$\text{FPR}(n,m,k)$$ (it is not). I have compared the values returned by `fpr_for` with numerical estimations of the FPR (after insertion, not the running FPR) and the level of agreement is excellent.

---

## Comment 2

> Username: Becheler  
> Created at: 2025-06-13 11:53:08 UTC  
> Url: https://github.com/boostorg/bloom/issues/19#issuecomment-2970157360  

Amazing work for the math derivation (not sure I can actually be a reliable reviewer on this one).  
  
Is it worth to make some semantics work around the running versus standard FPR ? I can't remember if you expose only the standard FPR to users, or if you enabled policy injection for tracking state in order to expose the running FPR. In which case those names could come in handy:  
  
- `steady_state_fpr`  
- `transient_fpr`  
  
What is your intention concerning this issue ? :)

---

## Comment 3

> Username: joaquintides  
> Created at: 2025-06-13 16:42:03 UTC  
> Url: https://github.com/boostorg/bloom/issues/19#issuecomment-2970929354  

I'd rather not provide this running FPR as a member function because  
* It doesn't seem to be in demand, and the literature does not mention it.  
* Calculation would be very very involved (see the scary formula) and I don't even know how to do it for filter configurations other than classical Bloom short of doing $$\sum_{i=0}^{n} \text{FPR}(i, m, k)$$, which is prohibitively expensive.

---

## Comment 4

> Username: Becheler  
> Created at: 2025-06-13 16:47:08 UTC  
> Url: https://github.com/boostorg/bloom/issues/19#issuecomment-2970945155  

I see. Then we can close this issue and remember it exists for further reference 🙂
