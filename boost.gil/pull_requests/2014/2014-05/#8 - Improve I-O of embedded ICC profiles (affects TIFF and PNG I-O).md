# #8 Improve I/O of embedded ICC profiles (affects TIFF and PNG I/O) [Merged]

> Username: gallafent  
> Created at: 2014-05-31 12:25:02 UTC  
> Updated at: 2014-06-13 01:27:21 UTC  
> Merged at: 2014-06-02 00:19:14 UTC  
> Closed at: 2014-06-02 00:19:14 UTC  
> Url: https://github.com/boostorg/gil/pull/8  

This adds the ability to load/save an embedded ICC profile to/from TIFF files, and makes some small improvements (not using std:: string to hold an array of bytes) to the PNG ICC load/save code.  
  
Also includes some of my previous fixes for TIFF I/O, and some improvements to those for clarity / readability / correctness.

---

## Comment 1

> Username: chhenning  
> Created_at: 2014-06-02 00:20:46 UTC  
> Url: https://github.com/boostorg/gil/pull/8#issuecomment-44793827  

I have pushed this to develop branch. Do you have some test images I could  
use for the unit test?  
  
Thanks for your help!  
  
On Sat, May 31, 2014 at 8:25 AM, gallafent notifications@github.com wrote:  
  
> This adds the ability to load/save an embedded ICC profile to/from TIFF  
> files, and makes some small improvements (not using std:: string to hold an  
> array of bytes) to the PNG ICC load/save code.  
>   
> Also includes some of my previous fixes for TIFF I/O, and some  
>   
> ## improvements to those for clarity / readability / correctness.  
>   
> You can merge this Pull Request by running  
>   
>   git pull https://github.com/gallafent/gil feature/icc-profile-io  
>   
> Or view, comment on, or merge it at:  
>   
>   https://github.com/boostorg/gil/pull/8  
> Commit Summary  
> - Get TIFF physical resolution load / save sorted out  
> - Neaten up multi-element property handling by using fusion vectors,  
>   and switch extrasamples property to work in this way. Add ability to load  
>   and save ICC colour profile information.  
> - Minor changes for extraneous duplicate line, and constness  
> - Improve handling of multi-valued properties in TIFF, and fix some  
>   problems with ICC profiles in PNG  
> - Minor header / indent correction  
> - whitespace  
> - Whitespace tidying, and default TIFF resolution (which is  
>   compulsory) to 1,1 (which I hope / think means square pixels of unknown  
>   size).  
> - Whitespace  
>   
> File Changes  
> - _M_ include/boost/gil/extension/io/formats/png/reader_backend.hpp  
>   https://github.com/boostorg/gil/pull/8/files#diff-0 (6)  
> - _M_ include/boost/gil/extension/io/formats/png/writer_backend.hpp  
>   https://github.com/boostorg/gil/pull/8/files#diff-1 (4)  
> - _M_ include/boost/gil/extension/io/formats/tiff/device.hpp  
>   https://github.com/boostorg/gil/pull/8/files#diff-2 (128)  
> - _M_ include/boost/gil/extension/io/formats/tiff/reader_backend.hpp  
>   https://github.com/boostorg/gil/pull/8/files#diff-3 (10)  
> - _M_ include/boost/gil/extension/io/formats/tiff/writer_backend.hpp  
>   https://github.com/boostorg/gil/pull/8/files#diff-4 (20)  
> - _M_ include/boost/gil/extension/io/png_tags.hpp  
>   https://github.com/boostorg/gil/pull/8/files#diff-5 (2)  
> - _M_ include/boost/gil/extension/io/tiff_tags.hpp  
>   https://github.com/boostorg/gil/pull/8/files#diff-6 (65)  
>   
> Patch Links:  
> - https://github.com/boostorg/gil/pull/8.patch  
> - https://github.com/boostorg/gil/pull/8.diff  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/gil/pull/8.

---

## Comment 2

> Username: chhenning  
> Created_at: 2014-06-02 00:28:47 UTC  
> Url: https://github.com/boostorg/gil/pull/8#issuecomment-44794034  

Would you be so kind and double check if the changes have been committed to  
github?  
  
On Sun, Jun 1, 2014 at 8:20 PM, Christian Henning chhenning@gmail.com  
wrote:  
  
> I have pushed this to develop branch. Do you have some test images I could  
> use for the unit test?  
>   
> Thanks for your help!  
>   
> On Sat, May 31, 2014 at 8:25 AM, gallafent notifications@github.com  
> wrote:  
>   
> > This adds the ability to load/save an embedded ICC profile to/from TIFF  
> > files, and makes some small improvements (not using std:: string to hold an  
> > array of bytes) to the PNG ICC load/save code.  
> >   
> > Also includes some of my previous fixes for TIFF I/O, and some  
> >   
> > ## improvements to those for clarity / readability / correctness.  
> >   
> > You can merge this Pull Request by running  
> >   
> >   git pull https://github.com/gallafent/gil feature/icc-profile-io  
> >   
> > Or view, comment on, or merge it at:  
> >   
> >   https://github.com/boostorg/gil/pull/8  
> > Commit Summary  
> > - Get TIFF physical resolution load / save sorted out  
> > - Neaten up multi-element property handling by using fusion vectors,  
> >   and switch extrasamples property to work in this way. Add ability to load  
> >   and save ICC colour profile information.  
> > - Minor changes for extraneous duplicate line, and constness  
> > - Improve handling of multi-valued properties in TIFF, and fix some  
> >   problems with ICC profiles in PNG  
> > - Minor header / indent correction  
> > - whitespace  
> > - Whitespace tidying, and default TIFF resolution (which is  
> >   compulsory) to 1,1 (which I hope / think means square pixels of unknown  
> >   size).  
> > - Whitespace  
> >   
> > File Changes  
> > - _M_ include/boost/gil/extension/io/formats/png/reader_backend.hpp  
> >   https://github.com/boostorg/gil/pull/8/files#diff-0 (6)  
> > - _M_ include/boost/gil/extension/io/formats/png/writer_backend.hpp  
> >   https://github.com/boostorg/gil/pull/8/files#diff-1 (4)  
> > - _M_ include/boost/gil/extension/io/formats/tiff/device.hpp  
> >   https://github.com/boostorg/gil/pull/8/files#diff-2 (128)  
> > - _M_ include/boost/gil/extension/io/formats/tiff/reader_backend.hpp  
> >   https://github.com/boostorg/gil/pull/8/files#diff-3 (10)  
> > - _M_ include/boost/gil/extension/io/formats/tiff/writer_backend.hpp  
> >   https://github.com/boostorg/gil/pull/8/files#diff-4 (20)  
> > - _M_ include/boost/gil/extension/io/png_tags.hpp  
> >   https://github.com/boostorg/gil/pull/8/files#diff-5 (2)  
> > - _M_ include/boost/gil/extension/io/tiff_tags.hpp  
> >   https://github.com/boostorg/gil/pull/8/files#diff-6 (65)  
> >   
> > Patch Links:  
> > - https://github.com/boostorg/gil/pull/8.patch  
> > - https://github.com/boostorg/gil/pull/8.diff  
> >   
> > —  
> > Reply to this email directly or view it on GitHub  
> > https://github.com/boostorg/gil/pull/8.

---
