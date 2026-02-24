# #43 Mitigate too noisy name-hiding warnings (C4453) on VS2015 Preview. [Merged]

> Username: Flast  
> Created at: 2014-11-19 08:02:09 UTC  
> Updated at: 2014-11-19 08:08:15 UTC  
> Merged at: 2014-11-19 08:04:28 UTC  
> Closed at: 2014-11-19 08:04:28 UTC  
> Url: https://github.com/boostorg/fusion/pull/43  

Split up from #39   
- Rename all preprocessed parameter name `_N` to `argN`.  
  - This PR doesn't contain preprocessed files to review easier. Please run preprocess after approved.  
  
In #39, @djowel pointed about parameter name changing of following two macros.  
- https://github.com/Flast/fusion/commit/9075da07901af6dd7aea832d90069454812382dc#diff-11fe1c6bdb7cc9504f6e858245ffb7adL73  
- https://github.com/Flast/fusion/commit/9075da07901af6dd7aea832d90069454812382dc#diff-549cabc0e4681240c0742c222bb8acebL76  
  - However, as I noted at https://github.com/boostorg/fusion/pull/39#discussion_r20518559, such changes are just for consistency with any other similar macros.

---

## Comment 1

> Username: djowel  
> Created_at: 2014-11-19 08:04:16 UTC  
> Url: https://github.com/boostorg/fusion/pull/43#issuecomment-63604706  

Nice!

---

## Comment 2

> Username: djowel  
> Created_at: 2014-11-19 08:05:04 UTC  
> Url: https://github.com/boostorg/fusion/pull/43#issuecomment-63604769  

does this close some Trac tickets?

---

## Comment 3

> Username: Flast  
> Created_at: 2014-11-19 08:08:13 UTC  
> Url: https://github.com/boostorg/fusion/pull/43#issuecomment-63604999  

No, this issue reported by @K-ballo at irc.

---
