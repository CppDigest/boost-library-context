# #457 Try handle about 60-mpfr and 1-bin-float warns [Merged]

> Username: ckormanyos  
> Created at: 2022-05-04 14:26:28 UTC  
> Updated at: 2022-05-05 04:26:52 UTC  
> Merged at: 2022-05-05 04:26:52 UTC  
> Closed at: 2022-05-05 04:26:52 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/457  



---

## Comment 1

> Username: ckormanyos  
> Created_at: 2022-05-04 14:28:56 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/457#issuecomment-1117384026  

We now turn our attention to mpfr float warnings. There are about 90 or maybe a hundred of them in the first set of GCC flags.  
  
- This PR handles the first batch of about 60 MPFR warns.  
- It also catches up with 1 remaining bin-float I/O warn.  
- There were about 10 or so MSVC level 4 warns.  
  
Let's see how this goes, then push through with the remaining MPFR warns in a future PR.  
  
After this, I might hang it up for a while and discuss future sensible steps, either more files of deeper, more-enhanced warnings.

---

## Review 2 [Commented]

> Username: jzmaddock  
> Created_at: 2022-05-04 18:39:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/457#pullrequestreview-962316242  

📁 include/boost/multiprecision/cpp_bin_float/io.hpp

```diff
 533 |       if (power10)
 534 |-          max_bits += (msb(boost::multiprecision::detail::abs(power10)) / 8) * limb_bits;
 534 |+       {
```

> Username: jzmaddock  
> Created_at: 2022-05-04 18:39:34 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/457#discussion_r865153579  

Since max_bits can never be negative, would it simplify things to make it unsigned?  Or does that complicate things elsewhere?

> Username: ckormanyos  
> Created_at: 2022-05-04 20:06:26 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/457#discussion_r865298176  

Good point, John. I'll take a look when dealing with the next batch of MPFR warnings.  
This one showed up in bin-float, but a little rework will be no problem if it seems easier.  
I'll check it out.

> Username: ckormanyos  
> Created_at: 2022-05-05 04:26:26 UTC  
> Updated_at: 2022-05-05 04:26:27 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/457#discussion_r865546463  

Actually when I tried making `max_bits` to be unsigned, there were some errors and warnings like here in my [prototyping project CI](https://github.com/ckormanyos/boost_multiprecision_harden/actions/runs/2272093129).  
  
So I decided to leave it signed. I guess we could follow up later, but as it was previously signed, i thought it's OK.


---
