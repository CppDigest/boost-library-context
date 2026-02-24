# #507 - Is there a Beginner Tutorial? [Closed]

> Username: RDust4  
> Created at: 2020-07-15 06:57:11 UTC  
> Updated at: 2020-07-26 20:35:43 UTC  
> Closed at: 2020-07-17 12:42:59 UTC  
> Url: https://github.com/boostorg/gil/issues/507  

Hi!  
  
I am new to Boost GIL and I can't find beginner tutorials, so it is very difficult for me to find out how to do things.  
For example, I want to write pixels to rgba32_image_t and then I want to export it as a PNG file.   
I also want to read this PNG file, but I don't know how.  
  
Greetings, RD4  
  
  
EDIT:  
What I need is:  
 1. SetPixel  
 2. GetPixel  
 3. Saving to png  
 4. Loading png  
 5. Upscaling / Downscaling

---

## Comment 1

> Username: simmplecoder  
> Created at: 2020-07-15 08:26:15 UTC  
> Url: https://github.com/boostorg/gil/issues/507#issuecomment-658624799  

I recommend first reading through [docs](https://www.boost.org/doc/libs/1_73_0/libs/gil/doc/html/index.html), all boost libraries provide documentation pages. The docs should cover points through 1-4. For scaling there is Lanczos filter, though I believe I have seen bilinear sampler somewhere too.

---

## Comment 2

> Username: mloskot  
> Created at: 2020-07-26 20:35:43 UTC  
> Url: https://github.com/boostorg/gil/issues/507#issuecomment-664036938  

@RDust4 Check the video lecture too https://www.boost.org/doc/libs/1_73_0/libs/gil/doc/html/tutorial/video.html, browse examples in https://github.com/boostorg/gil/tree/develop/example   
  
Browse the Q&A-s available at https://stackoverflow.com/tags/boost-gil where your questions may already be covered.  
  
If you have a specific question, then ask on SO or https://lists.boost.org/boost-gil
