# #74 - Add ASCII output for 1D histograms [Closed]

> Username: HDembinski  
> Created at: 2018-09-07 15:00:33 UTC  
> Updated at: 2020-02-23 14:06:44 UTC  
> Closed at: 2019-10-01 10:24:15 UTC  
> Url: https://github.com/boostorg/histogram/issues/74  

Even in times of GPUs, ASCII art is still popular. Some users asked for simple ASCII output, e.g. like the one in uproot:  
```  
                  0                                                       2410.8  
                  +------------------------------------------------------------+  
[-inf, -3)   0    |                                                            |  
[-3, -2.4)   68   |**                                                          |  
[-2.4, -1.8) 285  |*******                                                     |  
[-1.8, -1.2) 755  |*******************                                         |  
[-1.2, -0.6) 1580 |***************************************                     |  
[-0.6, 0)    2296 |*********************************************************   |  
[0, 0.6)     2286 |*********************************************************   |  
[0.6, 1.2)   1570 |***************************************                     |  
[1.2, 1.8)   795  |********************                                        |  
[1.8, 2.4)   289  |*******                                                     |  
[2.4, 3)     76   |**                                                          |  
[3, inf]     0    |                                                            |  
                  +------------------------------------------------------------+  
```  
The question is what to do when you want to plot an N-dimensional histogram.

---

## Comment 1

> Username: przemb  
> Created at: 2019-05-04 13:01:59 UTC  
> Updated at: 2019-05-09 16:46:08 UTC  
> Url: https://github.com/boostorg/histogram/issues/74#issuecomment-489324947  

Hi,  
I would like to start working on this issue.   
[WIP]

---

## Comment 2

> Username: HDembinski  
> Created at: 2019-05-13 11:55:58 UTC  
> Url: https://github.com/boostorg/histogram/issues/74#issuecomment-491792351  

Cool, thanks!

---

## Comment 3

> Username: przemb  
> Created at: 2019-05-13 22:42:22 UTC  
> Url: https://github.com/boostorg/histogram/issues/74#issuecomment-492013088  

Hi,   
I have prepared an initial version of the algorithm. I am able to display below histograms:  
```  
               0                                                          5.3  
               +------------------------------------------------------------+  
[-inf, -0.5) 0 |                                                            |  
[-0.5, 0)    1 |***********                                                 |  
[0, 0.5)     5 |*********************************************************   |  
[0.5, 1)     3 |**********************************                          |  
[1, 1.5)     0 |                                                            |  
[1.5, 2)     1 |***********                                                 |  
[2, inf]     0 |                                                            |  
               +------------------------------------------------------------+  
  
  
                         0                                                         20.0  
                         +------------------------------------------------------------+  
[-inf, -5)            0  |                                                            |  
[-5, -4.14286)        19 |*********************************************************   |  
[-4.14286, -3.28571)  16 |************************************************            |  
[-3.28571, -2.42857)  12 |************************************                        |  
[-2.42857, -1.57143)  14 |******************************************                  |  
[-1.57143, -0.714286) 14 |******************************************                  |  
[-0.714286, 0.142857) 10 |******************************                              |  
[0.142857, 1)         15 |*********************************************               |  
[1, inf]              0  |                                                            |  
                         +------------------------------------------------------------+  
  
  
                  0                                                       1266.3  
                  +------------------------------------------------------------+  
[-inf, -3)   0    |                                                            |  
[-3, -2.4)   819  |**************************************                      |  
[-2.4, -1.8) 1181 |*******************************************************     |  
[-1.8, -1.2) 854  |****************************************                    |  
[-1.2, -0.6) 1162 |*******************************************************     |  
[-0.6, 0)    884  |*****************************************                   |  
[0, 0.6)     1037 |*************************************************           |  
[0.6, 1.2)   1020 |************************************************            |  
[1.2, 1.8)   1203 |*********************************************************   |  
[1.8, 2.4)   844  |***************************************                     |  
[2.4, 3)     996  |***********************************************             |  
[3, inf]     0    |                                                            |  
                  +------------------------------------------------------------+  
```  
I would like to clarify some details:  
1. For a range of floats (in parenthesis), what is preferred precision? Is it always one decimal place? (second histogram)  
3. If the maximum value (top right corner, second histogram) is an integer, should it be presented with one decimal place  - _20.0_ - or just _20_?

---

## Comment 4

> Username: HDembinski  
> Created at: 2019-05-14 08:46:42 UTC  
> Url: https://github.com/boostorg/histogram/issues/74#issuecomment-492147119  

This looks great so far! Some suggestions.  
  
1. It would be nice to align the bin intervals vertically. To do that you should do one pass over bins to figure out the maximum of digits you need before and after the decimal point and then format all numbers consistently with that formatting instruction. Also keep in mind that some axis do not have bin intervals, but just values, like the axis::integer and axis::category. These need a different treatment.  
  
2. I would remove this number completely, which avoids the question.  
  
Can you please put the counts per bin on the right-hand-side of the ASCII art?

---

## Comment 5

> Username: HDembinski  
> Created at: 2019-05-14 08:51:06 UTC  
> Url: https://github.com/boostorg/histogram/issues/74#issuecomment-492148491  

This is what I have in mind:  
```  
             +------------------------------------------------------------+  
[-inf, -0.5) |                                                            | 0  
[-0.5,  0.0) |***********                                                 | 1  
[ 0.0,  0.5) |*********************************************************   | 5  
[ 0.5,  1.0) |**********************************                          | 3  
[ 1.0,  1.5) |                                                            | 0  
[ 1.5,  2.0) |***********                                                 | 1  
[ 2.0,  inf] |                                                            | 0  
             +------------------------------------------------------------+  
```

---

## Comment 6

> Username: przemb  
> Created at: 2019-06-02 22:17:02 UTC  
> Url: https://github.com/boostorg/histogram/issues/74#issuecomment-498070014  

Thank you for comments.   
What do you think about below visualizations?   
```  
Updated version:   
  
              +------------------------------------------------------------+  
[-inf, -0.5)  |                                                            | 0  
[-0.5,  0.0)  |***********                                                 | 1  
[ 0.0,  0.5)  |*********************************************************   | 5  
[ 0.5,  1.0)  |**********************************                          | 3  
[ 1.0,  1.5)  |                                                            | 0  
[ 1.5,  2.0)  |***********                                                 | 1  
[ 2.0,  inf]  |                                                            | 0  
              +------------------------------------------------------------+  
  
  
              +------------------------------------------------------------+  
[-inf, -5.0)  |                                                            | 0   
[-5.0, -4.1)  |*********************************************************   | 19  
[-4.1, -3.3)  |************************************************            | 16  
[-3.3, -2.4)  |************************************                        | 12  
[-2.4, -1.6)  |******************************************                  | 14  
[-1.6, -0.7)  |******************************************                  | 14  
[-0.7,  0.1)  |******************************                              | 10  
[ 0.1,  1.0)  |*********************************************               | 15  
[ 1.0,  inf]  |                                                            | 0   
              +------------------------------------------------------------+  
  
  
              +------------------------------------------------------------+  
[-inf, -3.0)  |                                                            | 0     
[-3.0, -2.4)  |**************************************                      | 819   
[-2.4, -1.8)  |*******************************************************     | 1181  
[-1.8, -1.2)  |****************************************                    | 854   
[-1.2, -0.6)  |*******************************************************     | 1162  
[-0.6,  0.0)  |*****************************************                   | 884   
[ 0.0,  0.6)  |*************************************************           | 1037  
[ 0.6,  1.2)  |************************************************            | 1020  
[ 1.2,  1.8)  |*********************************************************   | 1203  
[ 1.8,  2.4)  |***************************************                     | 844   
[ 2.4,  3.0)  |***********************************************             | 996   
[ 3.0,  inf]  |                                                            | 0     
              +------------------------------------------------------------+  
```  
  
```  
Alternative version:   
  
                +------------------------------------------------------------+  
[-inf, -0.5)  0 |                                                            |  
[-0.5,  0.0)  1 |***********                                                 |  
[ 0.0,  0.5)  5 |*********************************************************   |  
[ 0.5,  1.0)  3 |**********************************                          |  
[ 1.0,  1.5)  0 |                                                            |  
[ 1.5,  2.0)  1 |***********                                                 |  
[ 2.0,  inf]  0 |                                                            |  
                +------------------------------------------------------------+  
  
  
                 +------------------------------------------------------------+  
[-inf, -5.0)  0  |                                                            |  
[-5.0, -4.1)  19 |*********************************************************   |  
[-4.1, -3.3)  16 |************************************************            |  
[-3.3, -2.4)  12 |************************************                        |  
[-2.4, -1.6)  14 |******************************************                  |  
[-1.6, -0.7)  14 |******************************************                  |  
[-0.7,  0.1)  10 |******************************                              |  
[ 0.1,  1.0)  15 |*********************************************               |  
[ 1.0,  inf]  0  |                                                            |  
                 +------------------------------------------------------------+  
  
  
                   +------------------------------------------------------------+  
[-inf, -3.0)  0    |                                                            |  
[-3.0, -2.4)  819  |**************************************                      |  
[-2.4, -1.8)  1181 |*******************************************************     |  
[-1.8, -1.2)  854  |****************************************                    |  
[-1.2, -0.6)  1162 |*******************************************************     |  
[-0.6,  0.0)  884  |*****************************************                   |  
[ 0.0,  0.6)  1037 |*************************************************           |  
[ 0.6,  1.2)  1020 |************************************************            |  
[ 1.2,  1.8)  1203 |*********************************************************   |  
[ 1.8,  2.4)  844  |***************************************                     |  
[ 2.4,  3.0)  996  |***********************************************             |  
[ 3.0,  inf]  0    |                                                            |  
                   +------------------------------------------------------------+  
```

---

## Comment 7

> Username: HDembinski  
> Created at: 2019-06-03 10:19:26 UTC  
> Url: https://github.com/boostorg/histogram/issues/74#issuecomment-498199821  

Both look great! I am more inclined towards the "updated version" than the "alternative version" as you can imagine, but I will try to get some more feedback from other people. The mainline version sets the focus on the bars, the counts are an extra. The alternative version puts the focus on the counts.

---

## Comment 8

> Username: jonas-eschle  
> Created at: 2019-06-03 10:35:35 UTC  
> Url: https://github.com/boostorg/histogram/issues/74#issuecomment-498204528  

the "counts right" looks cleaner somehow, but less practical:  
_if_ I wanna look up a number, the "counts left" seems way easier to read out  
  
Tbh: no strong preference. But the longer I stare at it, the more I prefer the cleaner, "counts right" version.

---

## Comment 9

> Username: chrisburr  
> Created at: 2019-06-03 20:37:38 UTC  
> Url: https://github.com/boostorg/histogram/issues/74#issuecomment-498417152  

I would be tempted towards "alternative version" for the practicality of mapping a count to a bin.  
  
As another option, what about putting the counts on the far left so the bar and counts are both next to the interval:  
```  
Alternative 2 version:   
  
               +------------------------------------------------------------+  
0 [-inf, -0.5) |                                                            |  
1 [-0.5,  0.0) |***********                                                 |  
5 [ 0.0,  0.5) |*********************************************************   |  
3 [ 0.5,  1.0) |**********************************                          |  
0 [ 1.0,  1.5) |                                                            |  
1 [ 1.5,  2.0) |***********                                                 |  
0 [ 2.0,  inf] |                                                            |  
               +------------------------------------------------------------+  
  
  
                +------------------------------------------------------------+  
 0 [-inf, -5.0) |                                                            |  
19 [-5.0, -4.1) |*********************************************************   |  
16 [-4.1, -3.3) |************************************************            |  
12 [-3.3, -2.4) |************************************                        |  
14 [-2.4, -1.6) |******************************************                  |  
14 [-1.6, -0.7) |******************************************                  |  
10 [-0.7,  0.1) |******************************                              |  
15 [ 0.1,  1.0) |*********************************************               |  
 0 [ 1.0,  inf] |                                                            |  
                +------------------------------------------------------------+  
  
  
                  +------------------------------------------------------------+  
   0 [-inf, -3.0) |                                                            |  
 819 [-3.0, -2.4) |**************************************                      |  
1181 [-2.4, -1.8) |*******************************************************     |  
 854 [-1.8, -1.2) |****************************************                    |  
1162 [-1.2, -0.6) |*******************************************************     |  
 884 [-0.6,  0.0) |*****************************************                   |  
1037 [ 0.0,  0.6) |*************************************************           |  
1020 [ 0.6,  1.2) |************************************************            |  
1203 [ 1.2,  1.8) |*********************************************************   |  
 844 [ 1.8,  2.4) |***************************************                     |  
 996 [ 2.4,  3.0) |***********************************************             |  
   0 [ 3.0,  inf] |                                                            |  
                  +------------------------------------------------------------+  
```

---

## Comment 10

> Username: przemb  
> Created at: 2019-06-03 21:31:57 UTC  
> Updated at: 2019-06-03 21:53:48 UTC  
> Url: https://github.com/boostorg/histogram/issues/74#issuecomment-498434450  

What about putting counts "inside"?   
```  
              +------------------------------------------------------------+  
[-inf, -0.5)  | 0                                                          |  
[-0.5,  0.0)  |*********** 1                                               |  
[ 0.0,  0.5)  |********************************************************* 5 |  
[ 0.5,  1.0)  |********************************** 3                        |  
[ 1.0,  1.5)  | 0                                                          |  
[ 1.5,  2.0)  |*********** 1                                               |  
[ 2.0,  inf]  | 0                                                          |  
              +------------------------------------------------------------+  
   
              +-------------------------------------------------------------+  
[-inf, -5.0)  | 0                                                           |    
[-5.0, -4.1)  |********************************************************* 19 |  
[-4.1, -3.3)  |************************************************ 16          |  
[-3.3, -2.4)  |************************************ 12                      |  
[-2.4, -1.6)  |****************************************** 14                |  
[-1.6, -0.7)  |****************************************** 14                |  
[-0.7,  0.1)  |****************************** 10                            |  
[ 0.1,  1.0)  |********************************************* 15             |  
[ 1.0,  inf]  | 0                                                           |  
              +-------------------------------------------------------------+  
   
              +---------------------------------------------------------------+  
[-inf, -3.0)  | 0                                                             |      
[-3.0, -2.4)  |************************************** 819                     |    
[-2.4, -1.8)  |******************************************************* 1181   |  
[-1.8, -1.2)  |**************************************** 854                   |    
[-1.2, -0.6)  |******************************************************* 1162   |  
[-0.6,  0.0)  |***************************************** 884                  |    
[ 0.0,  0.6)  |************************************************* 1037         |  
[ 0.6,  1.2)  |************************************************ 1020          |  
[ 1.2,  1.8)  |********************************************************* 1203 |  
[ 1.8,  2.4)  |*************************************** 844                    |    
[ 2.4,  3.0)  |*********************************************** 996            |    
[ 3.0,  inf]  | 0                                                             |      
              +---------------------------------------------------------------+  
  
or maybe   
              +------------------------------------------------------------+  
[-inf, -0.5)  |                                                            |  
[-0.5,  0.0)  | 1 ********                                                 |  
[ 0.0,  0.5)  | 5 ******************************************************   |  
[ 0.5,  1.0)  | 3 *******************************                          |  
[ 1.0,  1.5)  |                                                            |  
[ 1.5,  2.0)  | 1 ***********                                              |  
[ 2.0,  inf]  |                                                            |  
              +------------------------------------------------------------+  
   
              +-------------------------------------------------------------+  
[-inf, -5.0)  |                                                             |    
[-5.0, -4.1)  | 19 *****************************************************    |  
[-4.1, -3.3)  | 16 ********************************************             |  
[-3.3, -2.4)  | 12 ********************************                         |  
[-2.4, -1.6)  | 14 **************************************                   |  
[-1.6, -0.7)  | 14 **************************************                   |  
[-0.7,  0.1)  | 10 **************************                               |  
[ 0.1,  1.0)  | 15 *****************************************                |  
[ 1.0,  inf]  |                                                             |  
              +-------------------------------------------------------------+  
   
              +---------------------------------------------------------------+  
[-inf, -3.0)  |                                                               |      
[-3.0, -2.4)  | 819 *********************************                         |    
[-2.4, -1.8)  | 1181 *************************************************        |  
[-1.8, -1.2)  | 854 ***********************************                       |    
[-1.2, -0.6)  | 1162 *************************************************        |  
[-0.6,  0.0)  | 884 ************************************                      |    
[ 0.0,  0.6)  | 1037 *******************************************              |  
[ 0.6,  1.2)  | 1020 ******************************************               |  
[ 1.2,  1.8)  | 1203 ***************************************************      |  
[ 1.8,  2.4)  | 844 **********************************                        |    
[ 2.4,  3.0)  | 996 ******************************************                |    
[ 3.0,  inf]  |                                                               |      
              +---------------------------------------------------------------+  
```

---

## Comment 11

> Username: HDembinski  
> Created at: 2019-06-04 08:32:33 UTC  
> Url: https://github.com/boostorg/histogram/issues/74#issuecomment-498576190  

Oh no, you guys added even more options! 😅  
  
@przemb Your first version of counts inside produces a very irregular image, I find that very irritating and strenuous to look at. The second option is better, but I don't like that bins without an entry don't get a zero. I would prefer your original "alternative version" over the new one.  
  
@chrisburr That's an interesting one, but it seems less obvious than the "alternative version" from @przemb. I think it is more natural to read from the left to right, starting with the bin interval first and then get a listing of the counts.  
  
Ok, all things considered, I would now favor the "alternative version":  
  
```  
  
                   +------------------------------------------------------------+  
[-inf, -3.0)  0    |                                                            |  
[-3.0, -2.4)  819  |**************************************                      |  
[-2.4, -1.8)  1181 |*******************************************************     |  
[-1.8, -1.2)  854  |****************************************                    |  
[-1.2, -0.6)  1162 |*******************************************************     |  
[-0.6,  0.0)  884  |*****************************************                   |  
[ 0.0,  0.6)  1037 |*************************************************           |  
[ 0.6,  1.2)  1020 |************************************************            |  
[ 1.2,  1.8)  1203 |*********************************************************   |  
[ 1.8,  2.4)  844  |***************************************                     |  
[ 2.4,  3.0)  996  |***********************************************             |  
[ 3.0,  inf]  0    |                                                            |  
                   +------------------------------------------------------------+  
```

---

## Comment 12

> Username: HDembinski  
> Created at: 2019-06-04 08:35:14 UTC  
> Url: https://github.com/boostorg/histogram/issues/74#issuecomment-498577107  

What's nice about that one: you can consider the character length of the printed numbers as another "bar", effectively you get a second log-scale view of the histogram for free.

---

## Comment 13

> Username: henryiii  
> Created at: 2019-06-05 21:12:25 UTC  
> Url: https://github.com/boostorg/histogram/issues/74#issuecomment-499257591  

You get a second log scale histogram anywhere you have the counts printed as long as they line up (though right aligned provides an odd backward histogram). I like the counts right, outside best, followed by the "alternative version" directly above. It's cleaner and less distracting I think to have them far right. I don't like the "inside" ones, as the endpoint of the bar is not clear.  
  
The more I look at it though, the more I like the "alternate" version - though it's not quite as clean, I think it is fairly simple to infer the meaning.

---

## Comment 14

> Username: HDembinski  
> Created at: 2019-06-06 10:47:17 UTC  
> Url: https://github.com/boostorg/histogram/issues/74#issuecomment-499445390  

@przemb So we have a winner, the "alternative version". Could you prepare a pull request so that I can review the implementation?

---

## Comment 15

> Username: przemb  
> Created at: 2019-06-06 18:56:56 UTC  
> Url: https://github.com/boostorg/histogram/issues/74#issuecomment-499623379  

Yes, sure. I would "polish" it, and I will make PR at the weekend.

---

## Comment 16

> Username: eduardo-rodrigues  
> Created at: 2019-06-07 10:22:07 UTC  
> Url: https://github.com/boostorg/histogram/issues/74#issuecomment-499834891  

Hi everyone, let me add to the party :): let me start by saying that I have no strong preference between the 2 ones that are getting more attraction. This being said, I do slightly prefer the version with the counts on the right, outside. After all, if I'm doing an ASCII representation of the plot, then I'm probably more interested in the looks than in the actual bin counts, which I can retrieve. Having the counts on the left, side-by-side with the bin definitions, is a bit distractive in that respect. This is me just thinking on why I would do an ASCII plot.

---

## Comment 17

> Username: HDembinski  
> Created at: 2019-06-07 12:49:04 UTC  
> Updated at: 2019-06-07 12:49:42 UTC  
> Url: https://github.com/boostorg/histogram/issues/74#issuecomment-499871898  

@eduardo-rodrigues I am happy to see that both you and Henry basically had the same preference as me, initially, but just like in Henry's case the version with counts on the left grew on me. What changed my mind is the following thought: this is an implementation for `std::cout << my_histogram`. We don't know what the user expects when they call this, but probably they also want to check the values. So we put the values in the center and provide the visualization as a bonus. It is easier to decipher this way.

---

## Comment 18

> Username: eduardo-rodrigues  
> Created at: 2019-06-07 12:51:19 UTC  
> Url: https://github.com/boostorg/histogram/issues/74#issuecomment-499872553  

I see. As said, no strong preference. Both versions will be "readable" :-).

---

## Comment 19

> Username: HDembinski  
> Created at: 2019-06-07 12:52:50 UTC  
> Url: https://github.com/boostorg/histogram/issues/74#issuecomment-499873009  

This would be Tufte's version with non-essential lines removed:  
```  
[-inf, -3.0)  0    |                                                              
[-3.0, -2.4)  819  |**************************************                        
[-2.4, -1.8)  1181 |*******************************************************       
[-1.8, -1.2)  854  |****************************************                      
[-1.2, -0.6)  1162 |*******************************************************       
[-0.6,  0.0)  884  |*****************************************                     
[ 0.0,  0.6)  1037 |*************************************************             
[ 0.6,  1.2)  1020 |************************************************              
[ 1.2,  1.8)  1203 |*********************************************************     
[ 1.8,  2.4)  844  |***************************************                       
[ 2.4,  3.0)  996  |***********************************************               
[ 3.0,  inf]  0    |                                                              
```

---

## Comment 20

> Username: jpivarski  
> Created at: 2019-06-09 01:22:17 UTC  
> Url: https://github.com/boostorg/histogram/issues/74#issuecomment-500177176  

Somehow, I didn't see this until now. I think it's great that you (@przemb) are seriously thinking about this issue! Here are my comments:  
  
   * Please put all numbers on the left. Take any one of the examples where the numbers were on the right or the ends of the bars and view it in a terminal that's narrower than the width of the bars. It gets messy, but it's still readable if the numbers all line up on the left: you can visually ignore the alternation that wrap-around bars cause, but can't find the numbers unless they all start on the same column near the left.  
   * Seeing a few histograms printed out one after another, it occurs to me that it could be useful to standardize the columns of text on the left—at least to give them a minimal width—so that they line up between histograms as well.  
   * I really like @HDembinski's "Tufte version." Not only would it be considerably easier to code (and therefore maintain), the visual simplicity is valuable in itself. I put the box around the plot to have a place to put a scale for what the asterisks mean, but that can be directly inferred from the maximum bin (or minimum and maximum if they straddle zero due to negative weights). More than just adhering to Tufte's gospel, the bounding box becomes a real problem when there's wrap-around.  
   * There are [Unicode box characters](https://en.wikipedia.org/wiki/Block_Elements#Character_table) (particularly 2588 to 258F) that would visually fill the space with more precision and less visual noise than asterisks, but pure ASCII should be a fall-back or a default.  
  
This isn't an ASCII histogram in the old sense (PAW's `h/print` attempted to draw something vertical, which utterly fell apart when your terminal wasn't very wide), it's a table with asterisks as a guide for the eye. When you see one of these things, you take in the general shape at a glance and use that to zoom in to the few numbers you really need to look at. It doesn't work when there are more bins than the number of lines in a terminal, like 100. It would therefore be great if an easy rebin/slice feeds into this: someone like me would be very productive if they could "zoom out" with a rebin and "zoom in" with a slice.  
  
If you're interested, Histogrammar had more cases: see [this documentation](https://github.com/histogrammar/histogrammar-docs/blob/master/tutorials/scala-basic/index.md#plotting-anything) and [this code](https://github.com/histogrammar/histogrammar-scala/blob/master/core/src/main/scala/org/dianahep/histogrammar/ascii.scala) (in Scala) for profile plots:  
  
```  
                        0.612726                                           0.796966  
                        +---------------------------------------------------------+  
underflow        nan    |                                                         |  
[  0   ,  3.33)  0.6432 |        |+-|                                             |  
[  3.33,  6.67)  0.6649 |               |+|                                       |  
[  6.67,  10  )  0.6803 |                    |+|                                  |  
[  10  ,  13.3)  0.6995 |                          |+|                            |  
[  13.3,  16.7)  0.7205 |                                 +|                      |  
[  16.7,  20  )  0.7345 |                                     |+                  |  
[  20  ,  23.3)  0.7439 |                                        |+               |  
[  23.3,  26.7)  0.7303 |                                    +|                   |  
[  26.7,  30  )  0.7139 |                               +|                        |  
[  30  ,  33.3)  0.6983 |                          +|                             |  
[  33.3,  36.7)  0.6791 |                    |+                                   |  
[  36.7,  40  )  0.6665 |                |+|                                      |  
[  40  ,  43.3)  0.6597 |              |+|                                        |  
[  43.3,  46.7)  0.6708 |                 |+|                                     |  
[  46.7,  50  )  0.6826 |                    |-+|                                 |  
[  50  ,  53.3)  0.6886 |                      |+-|                               |  
[  53.3,  56.7)  0.7105 |                             |+-|                        |  
[  56.7,  60  )  0.7318 |                                   |-+-|                 |  
[  60  ,  63.3)  0.7308 |                                   |-+-|                 |  
[  63.3,  66.7)  0.7259 |                                 |-+-|                   |  
[  66.7,  70  )  0.7498 |                                        |-+--|           |  
[  70  ,  73.3)  0.7535 |                                        |---+--|         |  
[  73.3,  76.7)  0.7482 |                                      |---+---|          |  
[  76.7,  80  )  0.7410 |                                   |----+---|            |  
[  80  ,  83.3)  0.7186 |                           |-----+----|                  |  
[  83.3,  86.7)  0.7510 |                                     |-----+-----|       |  
[  86.7,  90  )  0.7139 |                        |------+-------|                 |  
[  90  ,  93.3)  0.7117 |                      |--------+-------|                 |  
[  93.3,  96.7)  0.7532 |                                   |-------+--------|    |  
[  96.7,  100 )  0.6634 |     |----------+---------|                              |  
overflow         0.6428 |     |---+----|                                          |  
nanflow          nan    |                                                         |  
                        +---------------------------------------------------------+  
```  
  
and 2-dimensional histograms (this one could use a bounding box, and perhaps marginal projections on the left and bottom):  
  
```  
                          2                   1   
                                      1       1   
                  1   1   3   1   1   1               1   
      1       1   2   5   2   5   4   3   1   3   1   
          1   2   3   2   8  10   8   4   5   2   1   
      1       2   7   9   7  12   6   8   9   4   
      1   2   4   3  12  19  24  21  15  11   6   5       1   
      1   4   7   7  23  27  34  47  19  16  16   6   
      1   2   5   8  20  30  45  56  17  10   5   2   1   1   
      1   1   4   6   8  22  36  28  11   7   5   4       1   
      1           7  14  17  18  13   8   6   8   2   2   
  1       1   2   2   5  10   6   5   7   1   3   
          1   1       1       5   8   5   4   1   1   
              1   1           1   1   3   
                          2   3   
              1   
```  
  
Interestingly, I found [this discussion](https://root-forum.cern.ch/t/print-histograms-in-line-printer-format-on-screen/8318) on the ROOT forum in 2009. A user was looking for this feature because of a difficult ssh situation.

---

## Comment 21

> Username: HDembinski  
> Created at: 2019-06-10 10:58:43 UTC  
> Updated at: 2019-06-10 11:01:46 UTC  
> Url: https://github.com/boostorg/histogram/issues/74#issuecomment-500376777  

@jpivarski Thank you for these valuable comments! Your argument for putting the numbers on the left also makes a lot of sense to me. I like the suggestion to use unicode box characters, we can try to upgrade the plotting at a later point with this, if we figure out a way to detect whether the terminal supports unicode. The ASCII art has the advantage of being the lowest common denominator. I would therefore stick to that for now.  
  
Regarding the terminal width, this can be queried at runtime. It is difficult to do in a platform-independent manner, but the ncurses library and some programs manage to do it, so it is possible to adapt the width of the histograms to the current terminal width. Again I would worry about that later. For now I would like to move on with this feature.  
  
Regaring the plot Tufte-style, I put that here to document how it looks, but I am not clearly preferring it - at least if we can make sure that the terminal width is properly handled and bars never overflow into the next line. I am generally a fan of Tufte's minimalistic approach, but like any good thing it is also possible to overdo it. I can't find the article any more, but there was a blog entry where a professor tried a standard and a Tufte-rised histogram on his students. It turns out that the students liked the standard version better, because it had a frame around the plot. A frame is apparently a visual clue that grounds us, even when it does not provide quantitative information. Finally, it would be nice to follow a consistent style for histograms and profiles, which we both want to visualize, and the profile plot really needs the frame. So the histogram plot should also have it.

---

## Comment 22

> Username: HDembinski  
> Created at: 2019-06-10 11:04:18 UTC  
> Url: https://github.com/boostorg/histogram/issues/74#issuecomment-500378281  

There we go, tests for unicode support:  
https://rosettacode.org/wiki/Terminal_control/Unicode_output

---

## Comment 23

> Username: jpivarski  
> Created at: 2019-06-10 13:34:38 UTC  
> Url: https://github.com/boostorg/histogram/issues/74#issuecomment-500418616  

Actually, I'm not so sure I'd want Unicode to be automatically detected—I might choose pure ASCII on a terminal capable of Unicode because I plan to copy-paste the output. Maybe the choice of default could come down to auto-detection, though.  
  
I have a similar—and stronger—feeling about console width. You can try to auto-detect the width for the sake of a default, but there will be many circumstances where it's wrong. Passing through ssh, screen, Emacs's shell-mode, strange GUIs that have a terminal inside them, whatever Jupyter's terminal does, a VT100 emulator through Kermit—even if you find work-arounds for each of the cases I'm mentioning here, there will be others. The point of an ASCII histogram is that it's the least common denominator, and curses isn't the least common denominator (or at least, curses without being piped through something that curses doesn't know about).  
  
In short, I'm arguing that simplest is best—if the function produces standard output, always the same size, minimal decoration, users will work around it (particularly users who are asking for ASCII plots!).

---

## Comment 24

> Username: henryiii  
> Created at: 2019-06-10 14:02:18 UTC  
> Url: https://github.com/boostorg/histogram/issues/74#issuecomment-500427839  

I agree with Jim - Simpler is better as a default. Auto-detection is great, but you can always find a way to break it, so it should not be used in `operator <<` by default. You *could* have a helper "histogram printer" library for Boost.Histogram that allows a user to customize output formatting, such as with format strings similar to time formatting, or something more OO. But that would be much further down the line.  
  
And the wrapping issues sold me on numbers-on-the-left.

---

## Comment 25

> Username: HDembinski  
> Created at: 2019-06-11 06:38:13 UTC  
> Url: https://github.com/boostorg/histogram/issues/74#issuecomment-500707735  

No, *simpler* is not better as a default. *simple* is not a design criterion for a default at all. A default should work for most people, most of the time, and you cannot guess the terminal widths that people use. So setting a fixed width makes no sense. My terminals, for example, vary dramatically in width. Whatever fixed width you would chose, it would not work in some terminals. The width has to be autodetected by default, which is also what other terminal programs do, like everything that draws a progress bar, for example.  
  
You cannot pass options to `operator<<`, so it has to use a good default. `operator <<` will call a print function `std::ostream& stream(std::ostream& os, const histogram& h, unsigned terminal_width = 0)` (or similar name) where `terminal_width == 0` means auto-detect the width. People can then customize this if the autodetection fails.

---

## Comment 26

> Username: HDembinski  
> Created at: 2019-06-11 07:05:51 UTC  
> Url: https://github.com/boostorg/histogram/issues/74#issuecomment-500715073  

Two points to clarify. 1) When I speak about a *default*, I always mean a value that users can customize but which has a setting that works for most scenarios. 2) I didn't propose that Boost.Histogram should depend on the ncurses library to get the terminal width. Boost.Histogram may only depend on Boost and the C++ std lib. It can use the same logic that ncurses uses, though, to get the terminal width.

---

## Comment 27

> Username: jpivarski  
> Created at: 2019-06-11 10:38:24 UTC  
> Url: https://github.com/boostorg/histogram/issues/74#issuecomment-500786084  

I meant a fixed size with the understanding that it would be wrong on some terminal sizes. We're both addressing this issue of terminals being a variety of sizes, and my thinking was that it may be better to return the same width by default and be wrong by default than to try to figure it out and get into a weird corner case with curses or something.  
  
I didn't realize that this was to be the `<<` version of the histogram. I would have thought that this string should be something small that can fit inline (that what I do with `__repr__` as opposed to `__str__`), but maybe a multiline printout is not unusual for `<<`. Maybe auto-adjusting to screen width is not unusual for `<<`, so I'll of course leave it up to you.  
  
You're right that "simple" didn't always make good defaults—it should keep as many users as possible from having to touch the defaults, particularly if they'd have to switch modes to do so (like, from bare `<<` to a function call, which they'd have to look up). But if something has to be pulled apart to fit situations that even a complex default can't handle, it's often easier to pull apart simple things than complex ones. If the complexity is entirely in one parameter, like `width=magic` vs `width=80`, then it's easy to override and my point didn't apply. If the complexity is spread over many parameters, that's harder.

---

## Comment 28

> Username: HDembinski  
> Created at: 2019-10-01 10:24:15 UTC  
> Url: https://github.com/boostorg/histogram/issues/74#issuecomment-536972986  

Now implemented by @przemb who wrote an awesome patch! Thanks!

---

## Comment 29

> Username: mloskot  
> Created at: 2020-02-23 14:06:29 UTC  
> Updated at: 2020-02-23 14:06:44 UTC  
> Url: https://github.com/boostorg/histogram/issues/74#issuecomment-590072281  

Awesome work! Cross-referencing the PR #201
