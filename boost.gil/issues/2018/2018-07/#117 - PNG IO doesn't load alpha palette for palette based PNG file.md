# #117 - PNG IO doesn't load alpha palette for palette based PNG file [Closed]

> Username: adrianbroher  
> Created at: 2018-07-17 21:49:51 UTC  
> Updated at: 2019-08-07 20:35:50 UTC  
> Closed at: 2018-07-18 21:37:49 UTC  
> Url: https://github.com/boostorg/gil/issues/117  

In the FreeOrion project we use boost.gil to load PNG textures for our game. Previously we patched our own support for gray-alpha PNG images into the gil library.  However with merging the old development branch into master we now can finally rely on gil again.   
  
When porting the old FreeOrion code to the new v2 IO implementation for PNG I realized that it doesn't convert PNG files with a grayscale or indexed palette and an alpha palette (tRNS chunk) to a regular RGBA image as the previous implementation did.  
  
  
  
For example https://github.com/freeorion/freeorion/blob/master/default/data/art/icons/fleet/big-head-colony.png is a gray-alpha texture, which doesn't load properly.  
  
### Actual behavior  
  
![new-impl](https://user-images.githubusercontent.com/84891/42846263-b651283e-8a18-11e8-9923-70e09b00acd1.png)  
  
### Expected  behavior  
  
![old-impl](https://user-images.githubusercontent.com/84891/42846016-f2cac5b4-8a17-11e8-8a12-aee2a55e7aef.png)  
  
### Environment  
  
- Boost version: boostorg/gil master (boostorg/gil@c192814572d10605119ba47fcfcf25f62e1bdc42)  
  
  
Seems like querying `this->_info._num_trans > 0` is not the proper way to identify transparency information.  
  
https://github.com/boostorg/gil/blob/c192814572d10605119ba47fcfcf25f62e1bdc42/include/boost/gil/extension/io/png/detail/read.hpp#L124-L127  
  
In the FreeOrion project the corresponding code looks like:  
  
https://github.com/freeorion/freeorion/blob/a5eae2763800caa8dc322c8630bb9e9d975762a1/GG/src/gilext/io/png_io_private.hpp#L305-L312  
  
Is there a way to fix this before 1.68 release?  Should I create a pull request to fix this?

---

## Comment 1

> Username: chhenning  
> Created at: 2018-07-17 22:15:47 UTC  
> Url: https://github.com/boostorg/gil/issues/117#issuecomment-405746078  

Thanks for notifying us! It would be great if you could provide a pull request with a sample image. Such image we could add to the repository for testing.  
I'm not sure we can update boost master. It depends on how complicated the fix is.

---

## Comment 2

> Username: mloskot  
> Created at: 2019-07-16 17:04:07 UTC  
> Url: https://github.com/boostorg/gil/issues/117#issuecomment-511902390  

I've come back to this tRNS issue and #125 for a moment.  
Interestingly, the PNG file with a gray-alpha texture linked above, https://github.com/freeorion/freeorion/blob/master/default/data/art/icons/fleet/big-head-colony.png, does not seem to contain any `tRNS` chunks:  
  
![image](https://user-images.githubusercontent.com/80741/61314109-25d7af00-a7fc-11e9-9016-f448a90c5394.png)  
  
_On the screenshot, chunks are extracted using [png-chunk-extraction.go](https://github.com/parsiya/Go-Security/blob/8113f34759726503f60abc5d5f19eb659579a71f/png-tests/png-chunk-extraction.go) program as explained here https://parsiya.net/blog/2018-02-25-extracting-png-chunks-with-go/_
