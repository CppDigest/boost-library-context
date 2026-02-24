# #639 - Add support for WebP format? [Open]

> Username: striezel  
> Created at: 2022-03-28 00:22:02 UTC  
> Updated at: 2024-05-10 08:32:27 UTC  
> Url: https://github.com/boostorg/gil/issues/639  

### Is your feature request related to a problem? Please describe.  
  
GIL does not support images in the [WebP format](https://developers.google.com/speed/webp). In my experience, this format is becoming increasingly more common as a replacement for JPEG and PNG image formats. So at least read support for that format would be really nice. :)  
  
### Describe the solution you'd like  
  
There should be an I/O extension which can read WebP images (and possibly write such images, too).  
Easiest way would probably be to use the existing [libwebp library](https://chromium.googlesource.com/webm/libwebp/) to do the reading and writing. Encoding/decoding of such images in one go is possible via [libwebp's API](https://developers.google.com/speed/webp/docs/api#simple_decoding_api).  
  
### Questions  
  
* Are there any intentions to add WebP support to Boost GIL?  
  * If yes: Is someone already working on such a feature? What is the best way to support the existing efforts?  
  * If no: What is the suggested alternative for handling WebP with Boost GIL? Would a pull requests that adds support for WebP be welcome?

---

## Comment 1

> Username: mloskot  
> Created at: 2022-03-28 08:32:07 UTC  
> Url: https://github.com/boostorg/gil/issues/639#issuecomment-1080352740  

> Are there any intentions to add WebP support to Boost GIL?  
  
Yes, I think it's a very good idea to support the format.  
  
>  If yes: Is someone already working on such a feature?   
  
I'm not aware of any ongoing developments for this.  
  
> What is the best way to support the existing efforts?  
  
Since there are no ongoing efforts, the best way is to   
- look at how other formats are implemented, sources in `include/boost/gil/extension/io`  
- implement the I/O reader/writer for WebP  
- submit it via Pull Request for review  
- enjoy the fame and wealth ;-)

---

## Comment 2

> Username: ssleert  
> Created at: 2024-05-04 08:04:05 UTC  
> Url: https://github.com/boostorg/gil/issues/639#issuecomment-2094071319  

any progress?

---

## Comment 3

> Username: mloskot  
> Created at: 2024-05-04 15:53:40 UTC  
> Url: https://github.com/boostorg/gil/issues/639#issuecomment-2094272416  

Nope, none that I'm aware of.

---

## Comment 4

> Username: striezel  
> Created at: 2024-05-05 21:45:09 UTC  
> Url: https://github.com/boostorg/gil/issues/639#issuecomment-2094961508  

> any progress?  
  
No, [not really](https://github.com/boostorg/gil/compare/develop...striezel-stash:gil:extension-io-webp). ;)  
I still have the intention to implement it in the future, but it is currently _not_ one of my top priorities.  
  
If somebody really needs this urgently, then I could probably create a first iteration within the next one or two weeks. (Given that I find some spare time to dive into this.) The first iteration will probably be just able to read WebP images and have no writing capabilities, and it will probably only support reading into `rgb8_image_t` and `rgba8_image_t` images. Also remember that the WebP format allows for animated images (like animated GIFs), but all the GIL interfaces currently assume that one file means one image only. So animated WebP images would not work either.  
In other words: Don't expect too much from the first implementation.

---

## Comment 5

> Username: mloskot  
> Created at: 2024-05-10 08:32:26 UTC  
> Url: https://github.com/boostorg/gil/issues/639#issuecomment-2104184535  

> I still have the intention to implement it in the future, but it is currently not one of my top priorities.  
  
@striezel Thanks for the interest in working on this feature!
