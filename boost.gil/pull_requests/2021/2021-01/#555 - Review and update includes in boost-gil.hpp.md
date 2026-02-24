# #555 Review and update includes in boost/gil.hpp [Closed]

> Username: Siddharth1010  
> Created at: 2021-01-31 04:03:36 UTC  
> Updated at: 2021-02-03 02:49:02 UTC  
> Closed at: 2021-02-03 02:49:02 UTC  
> Url: https://github.com/boostorg/gil/pull/555  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
<!-- What does this pull request do? -->  
Fixes  #553 : Added missing headers and sorted them according to https://github.com/boostorg/gil/wiki/Include-Directives-Order

---

## Review 1 [Changes requested]

> Username: mloskot  
> Created_at: 2021-01-31 19:37:01 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/555#pullrequestreview-579926736  

Please, group headers based on folders/paths and order alphabetically within each group, e.g.:  
  
```  
#include <boost/gil/algorithm.hpp>  
#include <boost/gil/image_processing/adaptive_histogram_equalization.hpp>  
#include <boost/gil/image_processing/diffusion.hpp>  
#include <boost/gil/rasterization/circle.hpp>  
#include <boost/gil/rasterization/line.hpp>  
```

---

## Comment 2

> Username: Siddharth1010  
> Created_at: 2021-02-01 02:32:12 UTC  
> Url: https://github.com/boostorg/gil/pull/555#issuecomment-770522532  

According to the link, `#include <boost/gil/extension/*.hpp>` should be above `#include <boost/gil/*.hpp>` . So shouldn't `#include <boost/gil/algorithm.hpp>` be below `#include <boost/gil/rasterization/line.hpp>`  ?

---

## Comment 3

> Username: mloskot  
> Created_at: 2021-02-01 07:49:35 UTC  
> Url: https://github.com/boostorg/gil/pull/555#issuecomment-770647666  

No. The extensions are special, they're kind of external headers, not part of the  core library public headers.

---

## Comment 4

> Username: Siddharth1010  
> Created_at: 2021-02-01 07:58:02 UTC  
> Url: https://github.com/boostorg/gil/pull/555#issuecomment-770652502  

I have ordered all the headers alphabetically. Is this okay?

---

## Comment 5

> Username: mloskot  
> Created_at: 2021-02-01 19:08:40 UTC  
> Url: https://github.com/boostorg/gil/pull/555#issuecomment-771087662  

I have tweaked the order a bit, let's see if the CI builds complete

---

## Comment 6

> Username: Siddharth1010  
> Created_at: 2021-02-02 05:45:06 UTC  
> Url: https://github.com/boostorg/gil/pull/555#issuecomment-771379748  

Oh thank you. Have the checks failed again?

---

## Comment 7

> Username: mloskot  
> Created_at: 2021-02-02 19:55:56 UTC  
> Url: https://github.com/boostorg/gil/pull/555#issuecomment-771934584  

@Siddharth1010 Please, update your PR with latest `develop` branch from the upstream

---
