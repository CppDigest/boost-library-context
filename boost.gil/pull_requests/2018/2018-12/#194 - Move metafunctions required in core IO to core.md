# #194 [toolbox] Move metafunctions required in core IO to core [Open]

> Username: mloskot  
> Created at: 2018-12-16 00:08:26 UTC  
> Updated at: 2019-02-06 21:02:38 UTC  
> Url: https://github.com/boostorg/gil/pull/194  

**First stab** at removing the confusing core IO dependency on `toolbox` and `dynamic_image` extensions, moving four metafunctions together with their tests: `get_pixel_type`, `is_bit_aligned`, `is_homogeneous`, `pixel_bit_size`.  
  
Notice, the core IO dependency on the extensions still remains but it is not entirely clear: core IO uses `any_image_view` which seems to be pulled from `dynamic_image` indirectly through `toolbox` headers.  
  
If IO relies on `any_image` and `any_image_view`, those should be moved to core as well.  
  
### Tasklist  
  
- [ ] Review  
- [ ] Adjust for comments  
- [ ] All CI builds and checks have passed  
  
-----  
  
## Confusing dependency  
  
Regarding the existing confusion between the `toolbox` and the `dynamic_image`, the former seems to include the latter **just** to define this simple `get_pixel_type`:  
  
https://github.com/boostorg/gil/blob/32fec9f05ba8d558c7af7b1bd537c80f31c2b826/include/boost/gil/extension/toolbox/metafunctions/get_pixel_type.hpp#L26-L30  
  
pulls this which defines just a few tags  
  
https://github.com/boostorg/gil/blob/2250b7159c398899ec0fa27246f0d7aacdf2c1fe/include/boost/gil/extension/toolbox/dynamic_images.hpp#L18-L27  
  
then pulls `#include <boost/gil/extension/dynamic_image/dynamic_image_all.hpp>` to get the whole `dynamic_image` extension.  
  
## Plan proposal  
  
1. Merge this PR  
2. In current `gil/*.hpp` and `gil/io/*.hpp` core headers, find what else is required from `toolbox` and `dynamic_image`  
3. Move the required parts to the core headers.  
  
It means, the move direction should be from extensions to core, not the other way around, which would be much harder task, I think.

---

## Comment 1

> Username: mloskot  
> Created_at: 2018-12-17 17:45:05 UTC  
> Url: https://github.com/boostorg/gil/pull/194#issuecomment-447933862  

*** MARKING AS WIP ***  
  
On reflection, I'm postponing this until the whole `clang-tidy` refactoring is done. Moving files around while changing them may not be a good idea, esp. if we predict that is not all moving of files we may have to do :)

---
