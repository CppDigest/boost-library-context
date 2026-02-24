# #407 - Inconsistency in image and pixel types using floating point channels. [Open]

> Username: awulkiew  
> Created at: 2019-11-30 03:31:51 UTC  
> Updated at: 2019-11-30 14:57:08 UTC  
> Url: https://github.com/boostorg/gil/issues/407  

The typedefs for images and pixels using 32-bit and 64-bit floating point channel values are inconsistent. The 32-bit types defined in *boost/gil/typedefs.hpp* uses `boost::gil::scoped_channel_value<float, ...>` while 64-bit types defined in *boost/gil/io/typedefs.hpp* uses `double`.  
  
`float64_t` is defined in *boost/gil/typedefs.hpp* as `scoped_channel_value<double, ...>` so my guess is that this is the correct channel type for 64-bit images and pixels.

---

## Comment 1

> Username: mloskot  
> Created at: 2019-11-30 14:47:31 UTC  
> Updated at: 2019-11-30 14:57:08 UTC  
> Url: https://github.com/boostorg/gil/issues/407#issuecomment-559977300  

So, there is 64-bit floating-point type for channel defined using the `scoped_channel_value` _channel adaptor_:  
  
https://github.com/boostorg/gil/blob/ffdbf4e249260e43f6438177f1b7ebdee639fa03/include/boost/gil/typedefs.hpp#L128  
  
and this type will be used as `ChannelModel` in `pixel<ChannelModel, ...>`:  
  
https://github.com/boostorg/gil/blob/ffdbf4e249260e43f6438177f1b7ebdee639fa03/include/boost/gil/typedefs.hpp#L33  
  
while Core IO re-defines it using raw `double` type as the `ChannelModel`:  
  
https://github.com/boostorg/gil/blob/ffdbf4e249260e43f6438177f1b7ebdee639fa03/include/boost/gil/io/typedefs.hpp#L53  
  
This does look fishy! Thank you for reporting this.  
  
Any comments on that, @chhenning & @stefanseefeld?  
  
The only references in the Boost list to that adaptor are rather general in "scoped_channel_value type" https://lists.boost.org/Archives/boost//2010/07/169159.php and in the "image rescale algorithms", users make two interesting observations:  
  
1. https://lists.boost.org/Archives/boost//2009/09/156246.php  
    > The current state of rgba32f_pixel_t is meant to be scoped in the 0..1 range 'by contract'  
    > - even if GIL does not (currently) implement range checking  
  
2. https://lists.boost.org/Archives/boost//2009/09/156290.php  
    > I think rgba32f_pixel_t is a good model for hdr pixels.  
    > I don't see the 0..1 range in floating point channels as limits.  
    > And GIL does not enforce them in any way.  
    > They only define a mapping between integer and floating point channels.   
  
Those, however, apply to the core definitions.  
  
@chhenning is the main author of the new IO, so I hope he will be able to comment on the mismatches in `gil/io/typedefs.hpp`.
