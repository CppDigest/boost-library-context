# #167 Use constexpr instead of macros for tokenid constants in samples [Merged]

> Username: jefftrull  
> Created at: 2022-06-22 22:57:25 UTC  
> Updated at: 2022-06-23 00:35:45 UTC  
> Merged at: 2022-06-23 00:35:44 UTC  
> Closed at: 2022-06-23 00:35:44 UTC  
> Url: https://github.com/boostorg/wave/pull/167  

Under Visual Studio 2019 (and possibly earlier), building with `/permissive-` causes three samples to fail to compile. Adding the  
`/Zc:twoPhase-` option makes them compile successfully again. See bug #160 for more details.  
  
It's unclear what the source of this issue is, but using `constexpr` variables instead of a macro is better practice anyway, and fixes the errors.

---

## Review 1 [Approved]

> Username: hkaiser  
> Created_at: 2022-06-22 23:29:58 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/wave/pull/167#pullrequestreview-1016111811  

This LGTM, thanks. Shouldn't we however change all of the `token_id` definitions (at least for consistency's sake)?

---

## Comment 2

> Username: hkaiser  
> Created_at: 2022-06-22 23:32:16 UTC  
> Url: https://github.com/boostorg/wave/pull/167#issuecomment-1163760711  

> Shouldn't we however change all of the `token_id` definitions (at least for consistency's sake)?  
  
Nvm, I take the last part back.

---
