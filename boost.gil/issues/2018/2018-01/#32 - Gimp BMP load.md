# #32 - Gimp BMP load [Closed]

> Username: ohhmm  
> Created at: 2018-01-01 15:00:56 UTC  
> Updated at: 2018-01-02 18:34:50 UTC  
> Closed at: 2018-01-02 18:34:50 UTC  
> Url: https://github.com/boostorg/gil/issues/32  

Trying to load Gimp-saved BMP throws.  
io_error( "Invalid BMP info header." );  
The BMP:  
[gradient.bmp.zip](https://github.com/boostorg/gil/files/1596127/gradient.bmp.zip)  
  
Source PNG:  
![source png](https://user-images.githubusercontent.com/190779/34468597-ee93ece4-ef14-11e7-8c6a-0d1e554c1b8c.png)  
  
Using latest Gimp 2 at the moment 2.8.22 for MacOS

---

## Comment 1

> Username: ohhmm  
> Created at: 2018-01-01 15:19:18 UTC  
> Url: https://github.com/boostorg/gil/issues/32#issuecomment-354658450  

tried mspaint same  
could someone suggest an editor which would give loadable bmp, please?

---

## Comment 2

> Username: chhenning  
> Created at: 2018-01-02 18:34:42 UTC  
> Url: https://github.com/boostorg/gil/issues/32#issuecomment-354841620  

Gimp is using either v4 or v5 of bmp header formats. It's not quite clear which one.
