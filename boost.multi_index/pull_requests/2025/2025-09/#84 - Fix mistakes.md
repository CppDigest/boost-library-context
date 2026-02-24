# #84 Fix mistakes [Merged]

> Username: ivanpanch  
> Created at: 2025-09-07 15:22:03 UTC  
> Updated at: 2025-09-08 08:18:34 UTC  
> Merged at: 2025-09-08 08:18:24 UTC  
> Closed at: 2025-09-08 08:18:24 UTC  
> Url: https://github.com/boostorg/multi_index/pull/84  

I have corrected some minor mistakes.

---

## Review 1 [Commented]

> Username: joaquintides  
> Created_at: 2025-09-07 16:34:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multi_index/pull/84#pullrequestreview-3194516278  

📁 doc/reference/indices.html

```diff
 105 | guarantee the invariants associated to each index (e.g. some definite
 106 |- ordering,) elements of a <code>multi_index_container</code> are not mutable.
 106 |+ ordering) elements of a <code>multi_index_container</code> are not mutable.
```

> Username: joaquintides  
> Created_at: 2025-09-07 16:34:01 UTC  
> Url: https://github.com/boostorg/multi_index/pull/84#discussion_r2328757171  

I'd rather have "[...] ordering), elements[...]" here.

> Username: ivanpanch  
> Created_at: 2025-09-07 21:00:50 UTC  
> Url: https://github.com/boostorg/multi_index/pull/84#discussion_r2328843397  

Done!


---

## Review 2 [Commented]

> Username: joaquintides  
> Created_at: 2025-09-07 16:37:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multi_index/pull/84#pullrequestreview-3194517333  

📁 doc/reference/key_extraction.html

```diff
2080 | <code>composite_key_result&lt;composite_key&lt;K0,...,Kj> ></code>, with
2081 |- <code>Ki::result_type = Qi</code> for all <code>i = 0,...,j</code>.
2081 |+ <code>Ki::result_type = Qi</code> for all <code>i = 0,...,j</code>
```

> Username: joaquintides  
> Created_at: 2025-09-07 16:37:51 UTC  
> Updated_at: 2025-09-07 16:38:03 UTC  
> Url: https://github.com/boostorg/multi_index/pull/84#discussion_r2328758237  

Instead of eliminating the period, I'd replace with a comma (the statement is continued in "provided that each[...]").

> Username: ivanpanch  
> Created_at: 2025-09-07 21:00:55 UTC  
> Updated_at: 2025-09-07 21:00:56 UTC  
> Url: https://github.com/boostorg/multi_index/pull/84#discussion_r2328843562  

Done!


---

## Review 3 [Commented]

> Username: joaquintides  
> Created_at: 2025-09-07 16:39:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multi_index/pull/84#pullrequestreview-3194517716  

📁 doc/reference/key_extraction.html

```diff
2101 | <code>composite_key_result&lt;composite_key&lt;K0,...,Kk> ></code>, with
2102 |- <code>Ki::result_type = Qi</code> for all <code>i = 0,...,k</code>.
2102 |+ <code>Ki::result_type = Qi</code> for all <code>i = 0,...,k</code>
```

> Username: joaquintides  
> Created_at: 2025-09-07 16:39:17 UTC  
> Url: https://github.com/boostorg/multi_index/pull/84#discussion_r2328758636  

Again, I'd replace the period with a comma. Also, the clause  
  
<code>tuple&lt;Q0,...,Qk></code>, <code>k &lt;= n</code>  
  
is missing a final comma.

> Username: ivanpanch  
> Created_at: 2025-09-07 21:01:02 UTC  
> Url: https://github.com/boostorg/multi_index/pull/84#discussion_r2328843649  

Done!


---

## Review 4 [Commented]

> Username: joaquintides  
> Created_at: 2025-09-07 16:41:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multi_index/pull/84#pullrequestreview-3194518275  

📁 doc/reference/multi_index_container.html

```diff
1055 | <b>Requires:</b> <code>Value</code> is serializable (XML-serializable). Additionally,
1056 |- each of the indices of <code>m</code> can impose another requirements.<br>
1056 |+ each of the indices of <code>m</code> can impose another requirement.<br>
```

> Username: joaquintides  
> Created_at: 2025-09-07 16:41:31 UTC  
> Url: https://github.com/boostorg/multi_index/pull/84#discussion_r2328759358  

Incorrect fix: indices can impose more _several_ additional requirements, so the plurarl form is justified.

> Username: ivanpanch  
> Created_at: 2025-09-07 20:58:56 UTC  
> Url: https://github.com/boostorg/multi_index/pull/84#discussion_r2328842705  

Then it should say “other” instead of “another”. Yeah, I have thought about keeping the plural form instead (though the conjunction of all the requirements could be seen as a single requirement). Now fixed.


---

## Review 5 [Commented]

> Username: joaquintides  
> Created_at: 2025-09-07 16:41:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multi_index/pull/84#pullrequestreview-3194518372  

📁 doc/reference/multi_index_container.html

```diff
1065 | <b>Requires:</b> <code>Value</code> is serializable (XML-serializable). Additionally,
1066 |- each of the indices of <code>m'</code> can impose another requirements.<br>
1066 |+ each of the indices of <code>m'</code> can impose another requirement.<br>
```

> Username: joaquintides  
> Created_at: 2025-09-07 16:41:57 UTC  
> Url: https://github.com/boostorg/multi_index/pull/84#discussion_r2328759461  

Same as above, plural form was correct.

> Username: ivanpanch  
> Created_at: 2025-09-07 20:59:03 UTC  
> Url: https://github.com/boostorg/multi_index/pull/84#discussion_r2328842736  

Then it should say “other” instead of “another”. Yeah, I have thought about keeping the plural form instead (though the conjunction of all the requirements could be seen as a single requirement). Now fixed.


---

## Review 6 [Commented]

> Username: joaquintides  
> Created_at: 2025-09-07 16:43:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multi_index/pull/84#pullrequestreview-3194518764  

📁 doc/reference/rnd_indices.html

```diff
 180 | 
 181 |-   <li>The complexity of some operations, notably insertion and deletion, differ
 181 |+   <li>The complexity of some operations, notably insertion and deletion, differs
```

> Username: joaquintides  
> Created_at: 2025-09-07 16:43:17 UTC  
> Updated_at: 2025-09-07 16:43:18 UTC  
> Url: https://github.com/boostorg/multi_index/pull/84#discussion_r2328759847  

Re-reading this, I think this fix is better: "The complexities of some operations, notably insertion and deletion, differ [...]".

> Username: ivanpanch  
> Created_at: 2025-09-07 21:01:11 UTC  
> Url: https://github.com/boostorg/multi_index/pull/84#discussion_r2328843750  

Done!


---

## Comment 7

> Username: joaquintides  
> Created_at: 2025-09-08 08:18:34 UTC  
> Url: https://github.com/boostorg/multi_index/pull/84#issuecomment-3265125297  

Merged, thank you!

---
