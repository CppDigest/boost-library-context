# #774 Add further color map example [Merged]

> Username: jzmaddock  
> Created at: 2022-03-10 19:34:58 UTC  
> Updated at: 2024-01-24 21:34:02 UTC  
> Merged at: 2022-03-14 17:30:33 UTC  
> Closed at: 2022-03-14 17:30:34 UTC  
> Url: https://github.com/boostorg/math/pull/774  



---

## Comment 1

> Username: jzmaddock  
> Created_at: 2022-03-11 11:47:18 UTC  
> Url: https://github.com/boostorg/math/pull/774#issuecomment-1065040070  

@mborland @NAThompson FYI this is just me experimenting with the color map code, there's a new example which plots out some of the 2-arg special functions as color maps, really just to test the code out and see how useful it is.  
  
I ended up with plots like this:  
  
![extended_kindlmann_1F1](https://user-images.githubusercontent.com/5011768/157861048-dfe6c4ef-ad9c-4de7-98ba-2b92109f4fe1.png)  
  
Which has obviously had the scales etc added afterwards, but looks somewhat useful to add?

---

## Comment 2

> Username: mborland  
> Created_at: 2022-03-11 14:46:10 UTC  
> Url: https://github.com/boostorg/math/pull/774#issuecomment-1065179447  

> ... but looks somewhat useful to add?  
  
I think this is a useful addition. For the next release I can look into converting the examples from using `lodepng` to Boost.GIL or some other solution we can actually bundle.

---

## Comment 3

> Username: NAThompson  
> Created_at: 2022-03-11 16:43:43 UTC  
> Updated_at: 2022-03-11 16:47:16 UTC  
> Url: https://github.com/boostorg/math/pull/774#issuecomment-1065288858  

@jzmaddock : I'm a bit worried there; is it correct? I thought 1F1 was differentiable in b, but that looks discontinuous . . .  
  
Also, how did you add the scales? That might be a nice feature . . .

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2022-03-11 17:17:46 UTC  
> Url: https://github.com/boostorg/math/pull/774#issuecomment-1065318757  

>  I'm a bit worried there; is it correct? I thought 1F1 was differentiable in b, but that looks discontinuous . . .  
  
It's singular when `b = -k` for integer k.  
  
> Also, how did you add the scales? That might be a nice feature . . .  
  
It would be nice to do automatically, but I just edited in the GIMP.

---
