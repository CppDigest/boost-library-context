# #5 GIL TIFF and CMYK fixes [Merged]

> Username: gallafent  
> Created at: 2014-05-19 17:48:28 UTC  
> Updated at: 2014-07-10 11:27:14 UTC  
> Merged at: 2014-05-28 18:25:42 UTC  
> Closed at: 2014-05-28 18:25:42 UTC  
> Url: https://github.com/boostorg/gil/pull/5  

A couple of fixes:  
• change from \* to channel_multiply in the default colour converter for CMYK -> RGB.  
• Automatically infer photometric_interpretation tag from the image colour type /unless/ the user has manually specified one (I /think/ the original just has inverted logic here).  
  
These two bugs together allow the loading (and conversion into e.g. RGB) and saving of CMYK TIFF files to function correctly (neither was working before!)

---

## Comment 1

> Username: chhenning  
> Created_at: 2014-05-19 21:41:51 UTC  
> Url: https://github.com/boostorg/gil/pull/5#issuecomment-43562238  

Thanks, I'll have a look as soon as possible.  
  
Christian  
  
On Mon, May 19, 2014 at 1:48 PM, gallafent notifications@github.com wrote:  
  
> A couple of fixes:  
> • change from \* to channel_multiply in the default colour converter for  
> CMYK -> RGB.  
> • Automatically infer photometric_interpretation tag from the image colour  
> type /unless/ the user has manually specified one (I /think/ the original  
> just has inverted logic here).  
>   
> These two bugs together allow the loading (and conversion into e.g. RGB)  
> and saving of CMYK TIFF files to function correctly (neither was working  
>   
> ## before!)  
>   
> You can merge this Pull Request by running  
>   
>   git pull https://github.com/gallafent/gil tiff-cmyk  
>   
> Or view, comment on, or merge it at:  
>   
>   https://github.com/boostorg/gil/pull/5  
> Commit Summary  
> - Fix incorrect arithmetic in CMYK -> RGB conversion  
> - Fix inverted logic deciding whether to use the user's specified  
>   value, or a “guessed” value based on the pixel type of the image, when  
>   saving a TIFF.  
> - Match original indenting  
> - Actually match original indenting  
>   
> File Changes  
> - _M_ include/boost/gil/color_convert.hpphttps://github.com/boostorg/gil/pull/5/files#diff-0(6)  
> - _M_ include/boost/gil/extension/io/formats/tiff/writer_backend.hpphttps://github.com/boostorg/gil/pull/5/files#diff-1(14)  
>   
> Patch Links:  
> - https://github.com/boostorg/gil/pull/5.patch  
> - https://github.com/boostorg/gil/pull/5.diff  
>   
> —  
> Reply to this email directly or view it on GitHubhttps://github.com/boostorg/gil/pull/5  
> .

---

## Comment 2

> Username: chhenning  
> Created_at: 2014-05-28 18:27:09 UTC  
> Url: https://github.com/boostorg/gil/pull/5#issuecomment-44446102  

Changes have been applied.  
  
On Mon, May 19, 2014 at 5:41 PM, Christian Henning chhenning@gmail.comwrote:  
  
> Thanks, I'll have a look as soon as possible.  
>   
> Christian  
>   
> On Mon, May 19, 2014 at 1:48 PM, gallafent notifications@github.comwrote:  
>   
> > A couple of fixes:  
> > • change from \* to channel_multiply in the default colour converter for  
> > CMYK -> RGB.  
> > • Automatically infer photometric_interpretation tag from the image  
> > colour type /unless/ the user has manually specified one (I /think/ the  
> > original just has inverted logic here).  
> >   
> > These two bugs together allow the loading (and conversion into e.g. RGB)  
> > and saving of CMYK TIFF files to function correctly (neither was working  
> >   
> > ## before!)  
> >   
> > You can merge this Pull Request by running  
> >   
> >   git pull https://github.com/gallafent/gil tiff-cmyk  
> >   
> > Or view, comment on, or merge it at:  
> >   
> >   https://github.com/boostorg/gil/pull/5  
> > Commit Summary  
> > - Fix incorrect arithmetic in CMYK -> RGB conversion  
> > - Fix inverted logic deciding whether to use the user's specified  
> >   value, or a “guessed” value based on the pixel type of the image, when  
> >   saving a TIFF.  
> > - Match original indenting  
> > - Actually match original indenting  
> >   
> > File Changes  
> > - _M_ include/boost/gil/color_convert.hpphttps://github.com/boostorg/gil/pull/5/files#diff-0(6)  
> > - _M_ include/boost/gil/extension/io/formats/tiff/writer_backend.hpphttps://github.com/boostorg/gil/pull/5/files#diff-1(14)  
> >   
> > Patch Links:  
> > - https://github.com/boostorg/gil/pull/5.patch  
> > - https://github.com/boostorg/gil/pull/5.diff  
> >   
> > —  
> > Reply to this email directly or view it on GitHubhttps://github.com/boostorg/gil/pull/5  
> > .

---
