# #6 Allow images with alpha channel to be saved as TIFF [Merged]

> Username: gallafent  
> Created at: 2014-05-27 12:39:32 UTC  
> Updated at: 2014-06-13 01:27:21 UTC  
> Merged at: 2014-05-28 18:29:20 UTC  
> Closed at: 2014-05-28 18:29:20 UTC  
> Url: https://github.com/boostorg/gil/pull/6  

Here's another one, maybe not yet quite ready for merging, but I'd welcome your thoughts. It allows RGBA images to be saved into TIFF files. It includes some bits from my previous pull requests too, since this is the working codebase I'm using at the moment!  
To do this we make TIFF be saved with its colour channels premultiplied by the alpha channel, and set the appropriate TIFF tag for the extra channel.  
In particular, is the premultiplier sufficent / complete enough for general use (it Works For Me™)?  
Are the additions to the tags to allow multiple values appropriate (should I go further and make the type for each property /always/ be an MPL vector, forcing even single-valued properties' types to be wrapped into a single-element vector? That might make for clearer/simpler code in the write function, but is more typing for each tag which is added!)?  
Any other thoughts or suggestions?

---

## Comment 1

> Username: chhenning  
> Created_at: 2014-05-28 18:29:40 UTC  
> Url: https://github.com/boostorg/gil/pull/6#issuecomment-44446436  

Done.  
  
On Tue, May 27, 2014 at 8:39 AM, gallafent notifications@github.com wrote:  
  
> Here's another one, maybe not yet quite ready for merging, but I'd welcome  
> your thoughts. It allows RGBA images to be saved into TIFF files. It  
> includes some bits from my previous pull requests too, since this is the  
> working codebase I'm using at the moment!  
> To do this we make TIFF be saved with its colour channels premultiplied by  
> the alpha channel, and set the appropriate TIFF tag for the extra channel.  
> In particular, is the premultiplier sufficent / complete enough for  
> general use (it Works For Me™)?  
> Are the additions to the tags to allow multiple values appropriate (should  
> I go further and make the type for each property /always/ be an MPL vector,  
> forcing even single-valued properties' types to be wrapped into a  
> single-element vector? That might make for clearer/simpler code in the  
> write function, but is more typing for each tag which is added!)?  
>   
> ## Any other thoughts or suggestions?  
>   
> You can merge this Pull Request by running  
>   
>   git pull https://github.com/gallafent/gil tiff-alpha-save  
>   
> Or view, comment on, or merge it at:  
>   
>   https://github.com/boostorg/gil/pull/6  
> Commit Summary  
> - Remove duplicate definition of this_t in pnm format's scanline_reader  
> - Fix incorrect arithmetic in CMYK -> RGB conversion  
> - Fix inverted logic deciding whether to use the user's specified  
>   value, or a “guessed” value based on the pixel type of the image, when  
>   saving a TIFF.  
> - Match original indenting  
> - Actually match original indenting  
> - Merge branch 'tiff-cmyk' into develop  
> - First bit of getting alpha channels saved in TIFF files. The Extra  
>   Samples tag is now set.  
> - Initial implementation of premultiplier, which is used when saving  
>   TIFF (since by convention this uses premultiplied alpha)  
> - Allow more complex properties (with more than one element) to be set  
>   when saving TIFF.  
>   
> File Changes  
> - _M_ include/boost/gil/color_convert.hpphttps://github.com/boostorg/gil/pull/6/files#diff-0(6)  
> - _M_ include/boost/gil/extension/io/formats/pnm/scanline_read.hpphttps://github.com/boostorg/gil/pull/6/files#diff-1(6)  
> - _M_ include/boost/gil/extension/io/formats/tiff/device.hpphttps://github.com/boostorg/gil/pull/6/files#diff-2(41)  
> - _M_ include/boost/gil/extension/io/formats/tiff/write.hpphttps://github.com/boostorg/gil/pull/6/files#diff-3(41)  
> - _M_ include/boost/gil/extension/io/formats/tiff/writer_backend.hpphttps://github.com/boostorg/gil/pull/6/files#diff-4(25)  
> - _M_ include/boost/gil/extension/io/tiff_tags.hpphttps://github.com/boostorg/gil/pull/6/files#diff-5(20)  
> - _A_ include/boost/gil/premultiply.hpphttps://github.com/boostorg/gil/pull/6/files#diff-6(77)  
>   
> Patch Links:  
> - https://github.com/boostorg/gil/pull/6.patch  
> - https://github.com/boostorg/gil/pull/6.diff  
>   
> —  
> Reply to this email directly or view it on GitHubhttps://github.com/boostorg/gil/pull/6  
> .

---
