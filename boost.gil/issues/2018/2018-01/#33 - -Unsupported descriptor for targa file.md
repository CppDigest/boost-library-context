# #33 - "Unsupported descriptor for targa file" [Closed]

> Username: ohhmm  
> Created at: 2018-01-04 19:24:30 UTC  
> Updated at: 2018-03-07 18:45:37 UTC  
> Closed at: 2018-03-07 18:45:37 UTC  
> Url: https://github.com/boostorg/gil/issues/33  

Resized by macOS image viewer.  
  
[gresized.tga.zip](https://github.com/boostorg/gil/files/1604457/gresized.tga.zip)

---

## Comment 1

> Username: chhenning  
> Created at: 2018-01-04 20:37:07 UTC  
> Url: https://github.com/boostorg/gil/issues/33#issuecomment-355391939  

It appears that macOS image viewer is setting the 5th bit in the image descriptor. That means "Origin in upper left-hand corner.", according to http://www.paulbourke.net/dataformats/tga/  
  
It should work now.

---

## Comment 2

> Username: ohhmm  
> Created at: 2018-01-04 21:27:45 UTC  
> Url: https://github.com/boostorg/gil/issues/33#issuecomment-355403570  

"Image types aren't compatible."

---

## Comment 3

> Username: chhenning  
> Created at: 2018-01-04 21:58:33 UTC  
> Url: https://github.com/boostorg/gil/issues/33#issuecomment-355410851  

Your file is 32bit that mean you have to use rgba8_image_t in conjunction with read_image.
