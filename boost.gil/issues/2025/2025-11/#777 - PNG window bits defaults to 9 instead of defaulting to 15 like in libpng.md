# #777 - PNG window bits defaults to 9 instead of defaulting to 15 like in libpng. [Open]

> Username: domgetter  
> Created at: 2025-11-21 21:08:03 UTC  
> Updated at: 2025-11-21 21:20:13 UTC  
> Url: https://github.com/boostorg/gil/issues/777  

https://github.com/boostorg/gil/blob/d8c56358fae6302dd2f2e8cc496bcd4535c16183/include/boost/gil/extension/io/png/tags.hpp#L218  
  
The default window bits here should be 15 for a 32k window, to match libpng.  Having it set to 9 makes compression much much slower for anyone who uses png_tag with write_view, and there is no stated reason for why this was set to 9.

---

## Comment 1

> Username: mloskot  
> Created at: 2025-11-21 21:20:13 UTC  
> Url: https://github.com/boostorg/gil/issues/777#issuecomment-3564624376  

Possibly, I don't know, that default changed in libgpng. Please, feel encouraged to submit PR.
