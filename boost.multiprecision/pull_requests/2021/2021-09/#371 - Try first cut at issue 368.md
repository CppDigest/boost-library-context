# #371 Try first cut at issue 368 [Closed]

> Username: ckormanyos  
> Created at: 2021-09-21 14:31:02 UTC  
> Updated at: 2024-02-11 19:25:57 UTC  
> Closed at: 2024-02-11 19:25:56 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/371  

This PR attempts to address #368 with minimally non-intrusive changes of rounding in selected (but not all) places.

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2021-09-23 08:53:52 UTC  
> Updated_at: 2021-09-23 08:54:42 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/371#issuecomment-925622347  

Hi @jzmaddock what's your opinion on this PR?  
  
I introduced rounding-up in selected areas only including `floor`/`ceil`/cast-to-built-in-integral.  
  
Advantages include:  
- Handle the matter of #368 and possibly a subclass of related issues.  
- Introduce some rounding into `cpp_dec_float` to be expanded later.  
- Gain experience.  
  
Disadvantage is that rounding has not yet been successfully applied to arithmetic operations and there are some open questions regarding the best way to do this. This could/should be completed within the framework of also refining equality on the sub-limb level.  
  
At the moment, however, there are no tangible disadvantages other than items that were already missing anyway. And CI is passing (except Circle CI has been spinning around for a day or so).  
  
So what do you think of this PR, its scope of change and if we should actually merge?

---

## Review 2 [Commented]

> Username: jzmaddock  
> Created_at: 2021-09-24 18:25:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/371#pullrequestreview-763365189  

📁 test/git_issue_368.cpp

```diff
  29 |+         const std::string str_b = boost::lexical_cast<std::string>(b);
  30 |+ 
  31 |+         result_is_ok &= (str_a == str_b);
```

> Username: jzmaddock  
> Created_at: 2021-09-24 18:25:12 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/371#discussion_r715822265  

Questions: why are we comparing strings rather than values, and should we also test inputs which are not integer?  Negative values as well, plus floor too I suspect ;)

> Username: ckormanyos  
> Created_at: 2021-09-27 17:55:44 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/371#discussion_r716919195  

> why are we comparing strings rather than values,   
  
In this PR, there is rounding on `floor`, `ceil` and cast-to-int only. I removed the use of strings. But the comparison with, say, the result of `floor` or `ceil` is only true if the value being compared is casted to int prior to comparison. This is now done without strings in the candidate code of the PR's test file.  
  
> we also test inputs which are not integer  
  
It seems like there are already-existing rounding tests which do this.  
  
> Negative values as well, plus floor too I suspect  
  
Understood. Done in resent push(es).


---

## Review 3 [Commented]

> Username: jzmaddock  
> Created_at: 2021-09-24 18:25:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/371#pullrequestreview-763365498  

📁 include/boost/multiprecision/cpp_dec_float.hpp

```diff
3249 |+       if(result.isint())
3250 |+       {
3251 |+          ;
```

> Username: jzmaddock  
> Created_at: 2021-09-24 18:25:34 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/371#discussion_r715822489  

?

> Username: ckormanyos  
> Created_at: 2021-09-27 17:56:36 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/371#discussion_r716919810  

> `if(result.isint())`  
> ?  
  
Indeed. These sequences have been simlified in recent push(es).


---

## Review 4 [Commented]

> Username: jzmaddock  
> Created_at: 2021-09-24 18:28:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/371#pullrequestreview-763368012  

📁 include/boost/multiprecision/cpp_dec_float.hpp

```diff
 536 |+          local_limb_type tmp_limb_0 = data[0U];
 537 |+ 
 538 |+          // Manually count the number of base-10 digits on the zero'th limb.
```

> Username: jzmaddock  
> Created_at: 2021-09-24 18:28:48 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/371#discussion_r715824437  

This is the bit that caused me to assume that correct round was next to impossible to do efficiently in this type, but maybe it's not so bad?  I can't help but wonder if there are more efficient implementations - maybe binary search comparisons?  Or can we even do table lookup based on the most-significant-bit?  Just thinking out loud here....

> Username: ckormanyos  
> Created_at: 2021-09-27 17:58:38 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/371#discussion_r716921309  

> the bit that caused me to assume that correct round was next to impossible to do efficiently in this type  
  
Well, it sure is proving to be unruly. But there are some new efficiency provisions.  
- There was an existing binary search which had calculated the number of base-10 digits in the leading limb. This is used instead of a manual counting loop now.  
- For the power-of-10, a special non-`constexpr` table lookup has also been created for quick calculations of the power(s) of ten in similar matters.


---

## Comment 5

> Username: ckormanyos  
> Created_at: 2021-09-27 18:04:57 UTC  
> Updated_at: 2021-09-27 18:06:42 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/371#issuecomment-928136309  

Hi @jzmaddock I have attempted to address the review questions one-by-one.  
  
Personally, I am skeptical to introduce _partial_ rounding on `floor`, `ceil`, and cast-to-int only.  
  
The questions I ask myself are:  
  
1. Is `cpp_dec_float` worse off with partial rounding?  
2. Is `cpp_dec_float` neutral or about the same?  
3. Or does this partial rounding in this PR improve the overall class without high risk of breaking anything else?  
  
I tend to think we are around 2 and 3. I would, however, treat this skeptically. This PR does deal with #368 and a subset of similar issues. But it seems like a partial treatment of rounding, and partial could be interpreted as wrong or less right than uniform truncation.  
  
I ran some tests with rounding on arithmetic operators, but found that this leads to needing quite a few changes in tolerances and behaviors that are already possibly running. I am not sure if rounding over all (also on arithmetic operations) would break existing code or not? I also came to believe that rounding only makes sense if we combine it with a more exact reatment of equality (equality on the sub-limb, digit level). This has also been avoided over the years.  
  
I have no problem keeping these changes in the PR or letting it sit for a while, or even dropping this effort. Although a lot of folks are happily using `cpp_dec_float` in its current state, I am, however, somewhat motivated by the urge to deal with #368.  
  
Thoughts?

---
