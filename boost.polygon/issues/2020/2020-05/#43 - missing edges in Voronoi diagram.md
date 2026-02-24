# #43 - missing edges in Voronoi diagram [Open]

> Username: bubnikv  
> Created at: 2020-05-28 10:30:35 UTC  
> Updated at: 2021-03-22 17:39:34 UTC  
> Url: https://github.com/boostorg/polygon/issues/43  

This is a follow-up on https://svn.boost.org/trac10/ticket/12707  
where a smallest possible polygon has been distilled:  
  
        {       0, 10000000},  
        {  700000,        1}, // it has to be 1, higher number, zero or -1 work.  
        {  700000,  9000000},  
        { 9100000,  9000000},  
        { 9100000,        0},  
        {10000000, 10000000}  
  
I have rounded the coordinates to nice numbers for easier debugging.  
  
![image](https://user-images.githubusercontent.com/5830947/83130702-04637700-a0df-11ea-9a34-b57c279488df.png)

---

## Comment 1

> Username: bubnikv  
> Created at: 2020-05-28 10:42:13 UTC  
> Url: https://github.com/boostorg/polygon/issues/43#issuecomment-635262905  

The input polygon is turquoise  
black - inner voronoi lines  
green - outer voronoi lines  
red - voronoi parabolas  
  
Interestingly enough there is no Voronoi vertex placed into the right bottom concave vertex of the input polygon.  
  
I believe there is no notion in the documentation of boost::polygon Voronoi framework about the correctness of the algorithm. Luckily I found the following presentation  
https://www.youtube.com/watch?v=CU3VAOI0RL0&t=755s  
  
that mentions numerical robustness issues (starting with minute 14) and that the Voronoi circle is actually not precise, which will not crash the algorithm, but it will not necessarily produce correct output either. While for 32bit int input coordinates the Voronoi circle events are calculated with much higher accuracy than of the 32bit input coordinates, the accuracy is still not sufficient to guarantee topological correctness. I believe the example above is a demonstration of such a case. Often the topological errors are tiny and they do not cause any problem. Often the rounding of the Voronoi circle events may even produce cleaner output if many Voronoi edges intersect nearly at the same point. However, it seems to me that occasionally the topological errors caused by the imprecise Voronoi circle calculation are quite detrimental as in the example above. This behavior shall be documented. Ideally there would also be a sanity check available, which would detect these cases and possibly perturb the input coordinate, which i suppose will solve the issue with a very high probability.

---

## Comment 2

> Username: bubnikv  
> Created at: 2020-05-28 12:46:11 UTC  
> Url: https://github.com/boostorg/polygon/issues/43#issuecomment-635327947  

From the manual  
> https://www.boost.org/doc/libs/1_60_0/libs/polygon/doc/voronoi_main.htm  
  
> During the finalization step the implementation unites Voronoi vertices whose both coordinates are located within the relative error range equal to 128 machine epsilons and removes any Voronoi edges between those. **This is the only case, that might cause differences between the algorithm output topology and theoretically precise one**, and practically means the following: for a Voronoi diagram of a set of solid bodies inside the Solar System (radius 242 metres) and the long double (64 bit mantissa) output coordinate type the maximum absolute error within the Solar System rectangle will be equal to 2-64 * 242 * 26 = 2-18 metres; as the result, vertices with both coordinates that are within 2-18 metres (8 micrometres or the size of a bacteria) will be considered equal and united.  
  
The claim seems to be not correct, as changing order of Voronoi circle events may disturb the Voronoi diagram topology significantly, see the Luke Simonson's comment in the video referenced above. I believe the documentation needs to be adjusted.

---

## Comment 3

> Username: bubnikv  
> Created at: 2020-05-28 13:59:12 UTC  
> Url: https://github.com/boostorg/polygon/issues/43#issuecomment-635367670  

I just want to point out, that I find the library boost::polygon Voronoi extremely stable based on the number of issues reported. The majority of issues reported are due to intersections in input data, which I have collected and tested here:  
https://github.com/prusa3d/PrusaSlicer/blob/master/tests/libslic3r/test_voronoi.cpp  
  
https://svn.boost.org/trac10/ticket/12067  
seems to be a real issue, where a Voronoi line may not get generated before the 1st sweep line position and after the last sweep line position.  
  
https://svn.boost.org/trac10/ticket/12903  
Division by zero reported, but this issue is most likely a non-issue, as it produces an infinity for the interval of validity of the floating point calculation, therefore forcing a recalculation with extended accuracy.  
  
It would be great, if the library could detect intersections in the input data and report that instead of producing incorrect output. That would fix the major source of errors reported by the library users.

---

## Comment 4

> Username: asydorchuk  
> Created at: 2020-05-28 13:59:39 UTC  
> Url: https://github.com/boostorg/polygon/issues/43#issuecomment-635367939  

Hi Vojtěch,  
  
Thanks a lot for such detailed and great report! I will try to reproduce and look into more details this weekend.

---

## Comment 5

> Username: bubnikv  
> Created at: 2020-05-28 14:02:37 UTC  
> Url: https://github.com/boostorg/polygon/issues/43#issuecomment-635369668  

I would like to point out as well, that this Voronoi implementation was introduced to the world of desktop 3D printing by @aranel for his https://github.com/slic3r/slic3r, it is used by its fork http://github.com/prusa3d/prusaslicer, surely by https://github.com/ultimaker/cura, based on the results it generates, https://www.simplify3d.com/ seems to "borrow" @aranel 's idea and there are likely more.

---

## Comment 6

> Username: bubnikv  
> Created at: 2020-06-23 14:15:37 UTC  
> Updated at: 2020-06-24 13:20:26 UTC  
> Url: https://github.com/boostorg/polygon/issues/43#issuecomment-648178760  

> This polygon is even simpler and produces even more scary result.  
>   
>         { 0, 0 },  
>         { 70000000, 0 },  
>         { 70000000, 1300000 },  
>         { 70000007, 14000000 }  
  
Sorry, forge this one. I had a embarrassing bug in my visualization code causing integer oveflows.

---

## Comment 7

> Username: bubnikv  
> Created at: 2020-06-26 11:12:29 UTC  
> Url: https://github.com/boostorg/polygon/issues/43#issuecomment-650124236  

Here is a quad that seem to be related to the sample at the very top:  
  
shift = 35058881;  
a = -4 to 0: bug happens  
a = -5 and lower: ok  
a = 1 and higher: ok  
  
       { a + shift, -35000000 },  
       { a + shift, -25732145 },  
       { a + shift, -19586070 },  
       { -66716086 + shift, -35000000 }  
  
The issue seems to be triggered by collinear vertices. If I rotate the polygon, the Voronoi diagram is generated correctly. The same applies to the example at the very top, Voronoi diagram is generated correctly if the input is rotated.  
  
It seems like an incorrect Voronoi vertex is created where it should not have been (topologically it should be on a vertex of an input polygon, but it is quite far away), or some topological decision is being done, which then leads to an incorrect Voronoi vertex being taken. Both examples show a secondary Voronoi edge NOT connected to a vertex of an input contour.
