# #353 - [gsoc2019] Add tests for adaptive thresholding [Open]

> Username: mloskot  
> Created at: 2019-07-25 13:37:34 UTC  
> Updated at: 2021-01-12 18:33:20 UTC  
> Url: https://github.com/boostorg/gil/issues/353  

@miralshah365 has submitted implementation of the algorithm in #341 with example-as-test, which has worked fine for development, but we need more complete test suite for it.  
  
We need a basic variety of input images.  
  
### References  
  
Test images options are being discused arranged:  
- PNM format is going to move to the core features to be offered as built-in, see [[RFC] Managing input images for tests"](https://lists.boost.org/boost-gil/2019/06/0252.php)  
- Hosting of standard images for IO tests (and not only), see [[IO] Hosting test images](https://lists.boost.org/boost-gil/2019/07/0279.php)

---

## Comment 1

> Username: manav-yb  
> Created at: 2020-03-12 10:03:42 UTC  
> Url: https://github.com/boostorg/gil/issues/353#issuecomment-598103782  

Is there any work to be still done on this issue?

---

## Comment 2

> Username: mloskot  
> Created at: 2020-03-12 10:12:12 UTC  
> Url: https://github.com/boostorg/gil/issues/353#issuecomment-598107305  

AFAIK, yes, it an open issue and has not been targetted with any PR yet.

---

## Comment 3

> Username: AdityaSanthosh  
> Created at: 2021-01-09 06:33:22 UTC  
> Url: https://github.com/boostorg/gil/issues/353#issuecomment-757105575  

@mloskot So, we basically need to upload more PNM files in this repo https://github.com/chhenning/gil_io_images/tree/master/pnm by creating them using ImageMagick if we're going with Julia kind of maintaining test Images in external Repo. Is there any script to download them into my local boost gil repo? If not, what does it take to create such a script that deploys test images directly into my boost gil test folder?

---

## Comment 4

> Username: mloskot  
> Created at: 2021-01-12 18:33:20 UTC  
> Url: https://github.com/boostorg/gil/issues/353#issuecomment-758853989  

@AdityaSanthosh We maintain test images in this external repo under a GIL-dedicated organization https://github.com/boost-gil/test-images  
  
Next, we need a script (i.e. two scripts, in Bash and PowerShell) that download the images from that repo into explicitly specified or automatically determined known location e.g. `libs/gil/test/images`).  
  
Then, we need to   
- update the CI scripts to use those download scripts  
- modify tests to recognise the presence of the test images  
  
Finally, we can get rid of any test images hosted in the Boost.GIL repo or at least any binary images and just keep the ASCII PNM but that will be possible after https://github.com/boostorg/gil/issues/359 is implemented.  
  
--------  
  
Alternatively, we could Git submodule the test-images inside Boost.GIL repo, but I'm not convinced it is a good idea at all.
