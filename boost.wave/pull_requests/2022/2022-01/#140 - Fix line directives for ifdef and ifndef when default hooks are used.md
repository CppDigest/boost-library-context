# #140 Fix line directives for ifdef and ifndef when default hooks are used [Merged]

> Username: jefftrull  
> Created at: 2022-01-02 06:19:39 UTC  
> Updated at: 2022-01-02 15:09:33 UTC  
> Merged at: 2022-01-02 15:09:32 UTC  
> Closed at: 2022-01-02 15:09:33 UTC  
> Url: https://github.com/boostorg/wave/pull/140  

At some point in the past the handling for `#if` and `#ifdef` diverged. The code that handles emitting a line directive when a conditional section is skipped worked for `#if` but not `#ifdef/#ifndef`.  
  
This problem was not observable when the `eat_whitespace_hooks` were used instead of the `default_preprocessing_hooks`, because the former signals skipped newlines through the `may_skip_whitespace` hook, hiding the problem. Furthermore, the majority of Wave tests use the  
`eat_whitespace` hooks, so it wasn't visible in testing.  
  
This change restores `#ifdef/#ifndef` to the same section as `#if`, so any future changes to conditional handling will happen uniformly. Also, a test case is added to cover the default hooks and this particular case.  
  
If merged, this will resolve #138

---

## Review 1 [Approved]

> Username: hkaiser  
> Created_at: 2022-01-02 14:23:55 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/140#pullrequestreview-842418203  

LGTM, thanks!

---
