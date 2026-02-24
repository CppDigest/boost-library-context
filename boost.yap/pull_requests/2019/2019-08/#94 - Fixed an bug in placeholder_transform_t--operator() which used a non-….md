# #94 Fixed an bug in placeholder_transform_t::operator() which used a non-… [Merged]

> Username: pkeir  
> Created at: 2019-08-07 11:59:48 UTC  
> Updated at: 2019-08-11 02:27:04 UTC  
> Merged at: 2019-08-11 02:27:04 UTC  
> Closed at: 2019-08-11 02:27:04 UTC  
> Url: https://github.com/boostorg/yap/pull/94  

…existent value member of rvalue_mover. This allows replacements() and replace_placeholders() to be used. Also added a test file which checks these two, as well as evaluation().

---

## Comment 1

> Username: tzlaine  
> Created_at: 2019-08-11 02:26:36 UTC  
> Url: https://github.com/boostorg/yap/pull/94#issuecomment-520194713  

The stray "::type" was an obvious copy-paste error!  Thanks for catching this and for the fix.  The tests were especially welcome.

---
