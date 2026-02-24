# #440 - Intersection A-B and C-D is not equal to intersection B-A C-D [Open]

> Username: eyal0  
> Created at: 2017-12-22 13:41:04 UTC  
> Updated at: 2018-01-11 12:44:10 UTC  
> Url: https://github.com/boostorg/geometry/issues/440  

The intersection point returned depend on the direction of a segment.  The difference is usually just 1, probably due to rounding.   
  
For example, the intersection between this segment:  
  
`4710043 -2665034, 4685409 -2666385`  
  
with:  
  
`4700000 -2500000, 4700000 -3500000`  
  
Can be either:  
  
`4700000 -2665585`  
or  
`4700000 -2665584`  
  
depending on the order of the first segment.  
  
I would expect the order of the segment not to matter.

---

## Comment 1

> Username: aayushs98  
> Created at: 2017-12-24 08:36:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/440#issuecomment-353772577  

Hey eyal0, it seems that the intersection point in this example is non-integral.  
i.e- the PoI is actually: (4700000, -2665584.787245271)  
What should be ideally done when there is a non-integral PoI for examples like these?  
I am interested in further solving this bug, please guide on how to go about it.

---

## Comment 2

> Username: eyal0  
> Created at: 2017-12-26 14:41:48 UTC  
> Url: https://github.com/boostorg/geometry/issues/440#issuecomment-353975707  

Just pick one of them but consistently.  For example, do segment intersection in all 4 configurations and choose the result that has the lowest result.

---

## Comment 3

> Username: aayushs98  
> Created at: 2018-01-06 02:33:23 UTC  
> Url: https://github.com/boostorg/geometry/issues/440#issuecomment-355716840  

I'm willing to solve this issue. So could you please point me to the file that I can look forward to for solving this issue?

---

## Comment 4

> Username: eyal0  
> Created at: 2018-01-10 07:26:16 UTC  
> Url: https://github.com/boostorg/geometry/issues/440#issuecomment-356521406  

I don't know which file.  I'm not familiar with the boost code.  :-(  i think that it might be in the euclidean directory...?

---

## Comment 5

> Username: barendgehrels  
> Created at: 2018-01-10 14:25:49 UTC  
> Url: https://github.com/boostorg/geometry/issues/440#issuecomment-356617187  

Thanks - but it most probably is not so easy to fix, by just taking one file and make some edits. Besides that, the suggestion of doing segment intersection in all 4 configurations and taking the closest - will decrease performance by a factor 4 or so. We don't want that.  
  
The user, if he uses integer coordinates as input, can ideally (that is the idea of the library, hope it works in this case) output the intersection point in floating point and then round himself as wished.  
  
Alternatively, if the intersection point is indeed that mentioned floating point number, no matter of the order of calling, it actually should be rounded in the same way (which does not 4 calls) and that could be more easy to fix.

---

## Comment 6

> Username: eyal0  
> Created at: 2018-01-11 12:44:10 UTC  
> Url: https://github.com/boostorg/geometry/issues/440#issuecomment-356923505  

In the end, I did it with floating point and rounding. In floating point the result is different depending on the order of the segments but after rounding, the results were the same in the test case above.  
  
There might be a case where even doing it in floating point and then rounding still gives two different answers. I'm not sure. I would guess probably yes.
