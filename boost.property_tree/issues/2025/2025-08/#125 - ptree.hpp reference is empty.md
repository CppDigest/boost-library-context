# #125 - ptree.hpp reference is empty [Open]

> Username: LegalizeAdulthood  
> Created at: 2025-08-08 06:06:05 UTC  
> Updated at: 2025-08-10 04:33:13 UTC  
> Url: https://github.com/boostorg/property_tree/issues/125  

In particular, the doxygen for the `get_value` member function is missing, but there appears to be no doxygen output at all, only a link to the raw header.  
  
See https://www.boost.org/doc/libs/latest/doc/html/property_tree/reference.html#doxygen.ptree_8hpp

---

## Comment 1

> Username: ashtum  
> Created at: 2025-08-08 08:50:22 UTC  
> Url: https://github.com/boostorg/property_tree/issues/125#issuecomment-3167064501  

This is because Doxygen first finds it where it is forward declared, in: `<boost/property_tree/ptree_fwd.hpp>`. so, it appears under that header. I guess we can wrap it with `BOOST_PROPERTY_TREE_DOXYGEN_INVOKED` to hide it from Doxygen.

---

## Comment 2

> Username: LegalizeAdulthood  
> Created at: 2025-08-08 16:33:07 UTC  
> Url: https://github.com/boostorg/property_tree/issues/125#issuecomment-3168590474  

OK, but I still can't find anything for the `get_value` member function, even though `property_tree` is listed under `ptree_fwd.hpp`

---

## Comment 3

> Username: ashtum  
> Created at: 2025-08-08 16:55:11 UTC  
> Url: https://github.com/boostorg/property_tree/issues/125#issuecomment-3168699408  

> OK, but I still can't find anything for the `get_value` member function, even though `property_tree` is listed under `ptree_fwd.hpp`  
  
Do you mean the following?  
https://www.boost.org/doc/libs/latest/doc/html/doxygen/classboost_1_1property__tree_1_1basic__ptree.html#doxygen.classboost_1_1property__tree_1_1basic__ptree_1a2be8e39d3b23f68f6e338c0d020dbc5b

---

## Comment 4

> Username: LegalizeAdulthood  
> Created at: 2025-08-08 19:08:00 UTC  
> Url: https://github.com/boostorg/property_tree/issues/125#issuecomment-3169040285  

how do you find that from the reference section, though?  It's now shown anywhere in the reference pages that I saw

---

## Comment 5

> Username: ashtum  
> Created at: 2025-08-08 20:06:23 UTC  
> Updated at: 2025-08-08 20:29:11 UTC  
> Url: https://github.com/boostorg/property_tree/issues/125#issuecomment-3169158115  

All documented symbols in those code blocks are links, so you can click on them and go to their specific page.

---

## Comment 6

> Username: LegalizeAdulthood  
> Created at: 2025-08-10 04:33:02 UTC  
> Updated at: 2025-08-10 04:33:13 UTC  
> Url: https://github.com/boostorg/property_tree/issues/125#issuecomment-3172360282  

OK, but the text "get_value" does not appear on that page, therefore it can't be a link, which is the point of this bug report: information is missing.
