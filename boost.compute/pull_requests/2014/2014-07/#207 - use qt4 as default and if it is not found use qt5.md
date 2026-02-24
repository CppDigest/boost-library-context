# #207 use qt4 as default and if it is not found use qt5 [Closed]

> Username: f-koehler  
> Created at: 2014-07-31 12:34:50 UTC  
> Updated at: 2014-08-04 08:44:00 UTC  
> Closed at: 2014-08-04 08:44:00 UTC  
> Url: https://github.com/boostorg/compute/pull/207  

These CMake files look for QT4 in the first place and Qt5 in the second place. I tested it on Archlinux and Ubuntu using the following scenarios:  
- Qt5 installed -> works  
- Qt4 installed -> works  
- Qt4 and Qt5 installed -> works Qt4 is used  
- no Qt installed -> cmake gives an error  
  
The reason why I decided to look for Qt4 in the first place is that older versions of CMake do not necesseraliy come with a find scripts for Qt5. This would result in an error.

---

## Comment 1

> Username: coveralls  
> Created_at: 2014-07-31 12:48:40 UTC  
> Url: https://github.com/boostorg/compute/pull/207#issuecomment-50753715  

[![Coverage Status](https://coveralls.io/builds/1028060/badge)](https://coveralls.io/builds/1028060)  
  
Coverage remained the same when pulling **a36600d77808d142829996bb18df9ca2ff57ef38 on f-koehler:master** into **8a11a320d3bdc3e2b560fa35c5b67e4e0958a910 on kylelutz:develop**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2014-08-01 03:23:28 UTC  
> Updated_at: 2014-08-01 07:20:44 UTC  
> Url: https://github.com/boostorg/compute/pull/207#discussion_r15681250  

it would be good to keep this list outside the `if()` block so that we don't have to repeat it twice (which could lead to someone adding an example to only one and not the other). we could also do the same thing with the qimage_blur example and just make a list with a single item called `QT_EXAMPLES`. then the Qt4 and Qt5 side of the loop can just iterate over each list and set them up as necessary.

---

## Comment 3

> Username: kylelutz  
> Created_at: 2014-08-01 03:24:19 UTC  
> Url: https://github.com/boostorg/compute/pull/207#issuecomment-50845397  

Looking good! Left one inline comment. Thanks for working on this.

---

## Comment 4

> Username: coveralls  
> Created_at: 2014-08-01 07:17:50 UTC  
> Url: https://github.com/boostorg/compute/pull/207#issuecomment-50856088  

[![Coverage Status](https://coveralls.io/builds/1031809/badge)](https://coveralls.io/builds/1031809)  
  
Coverage increased (+0.37%) when pulling **36e4354e092961d7af2ea9994892201890bc8b2e on f-koehler:master** into **8a11a320d3bdc3e2b560fa35c5b67e4e0958a910 on kylelutz:develop**.

---

## Comment 5

> Username: f-koehler  
> Created_at: 2014-08-01 07:23:32 UTC  
> Url: https://github.com/boostorg/compute/pull/207#issuecomment-50856445  

This is much simpler.

---

## Comment 6

> Username: coveralls  
> Created_at: 2014-08-01 07:33:05 UTC  
> Url: https://github.com/boostorg/compute/pull/207#issuecomment-50857098  

[![Coverage Status](https://coveralls.io/builds/1031835/badge)](https://coveralls.io/builds/1031835)  
  
Coverage increased (+0.37%) when pulling **a7e9b5d23964a25fc08a367853b7c224e724a9ff on f-koehler:master** into **8a11a320d3bdc3e2b560fa35c5b67e4e0958a910 on kylelutz:develop**.

---

## Comment 7

> Username: kylelutz  
> Created_at: 2014-08-03 01:40:59 UTC  
> Url: https://github.com/boostorg/compute/pull/207#discussion_r15732589  

looks like the indentation got messed up here (tabs vs spaces?). usually i use two-spaces (no tabs) for indenting in cmake files.

---

## Comment 8

> Username: kylelutz  
> Created_at: 2014-08-03 01:43:04 UTC  
> Url: https://github.com/boostorg/compute/pull/207#issuecomment-50979699  

Cool! Tested it out with Qt4 on my machine and everything works. Could you just rebase this on top of my `master` branch? That makes the history a little easier to follow and review. After that it should be good to merge.

---
