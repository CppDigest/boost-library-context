# #293 Improve seq_parser to not give attributes to nope-producing parser [Closed]

> Username: andreasbuhr  
> Created at: 2025-11-16 06:23:31 UTC  
> Updated at: 2025-11-16 06:39:49 UTC  
> Closed at: 2025-11-16 06:39:48 UTC  
> Url: https://github.com/boostorg/parser/pull/293  

seq_parser gave one of the result attribute tuple parts to each subparser, even if that tuple part was not intended for that subparser and the subparser had attribute type nope. This commit changes seq_parsers to not give any attribute to subparsers with attribute type nope.

---

## Comment 1

> Username: andreasbuhr  
> Created_at: 2025-11-16 06:25:45 UTC  
> Url: https://github.com/boostorg/parser/pull/293#issuecomment-3538055167  

This should prevent things like https://github.com/boostorg/parser/issues/291, https://github.com/boostorg/parser/issues/279, and https://github.com/boostorg/parser/issues/285.  
  
In addition, any parser with attribute type nope should not touch its attribute if it is given one. But that should be handled elsewhere.

---

## Comment 2

> Username: andreasbuhr  
> Created_at: 2025-11-16 06:39:48 UTC  
> Url: https://github.com/boostorg/parser/pull/293#issuecomment-3538093669  

Sorry, my bad, this is fixed in develop. My repo was not up to date.

---
