# #252 Remove the implicit use of the regex_constants::match_any flag [Open]

> Username: zmysysz  
> Created at: 2025-05-26 07:10:15 UTC  
> Updated at: 2025-05-26 07:10:15 UTC  
> Url: https://github.com/boostorg/regex/pull/252  

Remove the implicit use of the regex_constants::match_any flag from regex_match and regex_search to improve performance in cases where match results are not needed. (#251)

---
