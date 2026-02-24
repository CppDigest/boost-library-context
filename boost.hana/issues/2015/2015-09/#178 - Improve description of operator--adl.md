# #178 - [Documentation] Improve description of operator::adl [Closed]

> Username: m-j-w  
> Created at: 2015-09-09 21:47:13 UTC  
> Updated at: 2015-09-09 23:37:03 UTC  
> Closed at: 2015-09-09 23:37:03 UTC  
> Url: https://github.com/boostorg/hana/issues/178  

Since being inherited so often, `operator::adl` deserves a bit more attention than "...enables ADL..." in the manual. Proposition to write out ADL as "... enables argument-dependent-lookup (ADL) ..." and add a reference to something like http://en.cppreference.com/w/cpp/language/adl. That would give a better hint of its purpose and significance.

---

## Comment 1

> Username: ldionne  
> Created at: 2015-09-09 22:20:51 UTC  
> Url: https://github.com/boostorg/hana/issues/178#issuecomment-139061423  

`operator::adl` (and in general the whole `operator` namespace is an implementation detail. Thanks for pointing the issue out, but I will instead move `operators::adl` to the `detail` namespace to make this clear.
