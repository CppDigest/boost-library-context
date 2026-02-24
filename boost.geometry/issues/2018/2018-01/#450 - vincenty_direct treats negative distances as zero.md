# #450 - vincenty_direct treats negative distances as zero [Closed]

> Username: cffk  
> Created at: 2018-01-14 23:06:51 UTC  
> Updated at: 2019-03-27 17:00:32 UTC  
> Closed at: 2019-03-27 17:00:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/450  

vincenty_direct includes  
  
        if ( math::equals(distance, Dist(0)) || distance < Dist(0) )  
        {  
            result.lon2 = lon1;  
            result.lat2 = lat1;  
            return result;  
        }  
i.e., negative distances are treated as zero.  
  
If it's really the case that distances must be non-negative then throw an error when this condition is violated; don't silently set the distance to zero.  
  
However, the notion that distances should be non-negative is a discredited prejudice.  See this passage from H. W. Eves, [College Geometry](http://books.google.com/books?id=B81gnTjNazMC&pg=PA50) (1995), p. 50:  
  
> 1.1 SENSED MAGNITUDES  
> One of the innovations of modern elementary geometry is the employment, when it proves useful, of sensed, or signed, magnitudes. It was the extension of the number system to include both positive and negative numbers that led to this forward step in geometry. Although Albert Girard, René Descartes, and others introduced negative segments into geometry during the seventeenth century, the idea of sensed magnitudes was first systematically exploited in the early nineteenth century by L. N. M. Carnot (in his [Géométrie de position](http://books.google.com/books?id=-DUVAAAAQAAJ) of 1803) and especially by A. F. Möbius (in his [Der barycentrische Calcul](http://books.google.com/books?id=eFPluv_UqFEC) of 1827).  By means of the concept of sensed magnitudes, several separate statements or relations can often by combined into a single embracive statement or relation, and a single proof can frequently be formulated for a theorem that would otherwise require the treatment of a number of different cases.

---

## Comment 1

> Username: asrdav  
> Created at: 2018-03-06 18:18:11 UTC  
> Url: https://github.com/boostorg/geometry/issues/450#issuecomment-370876950  

Is this bug still open?

---

## Comment 2

> Username: awulkiew  
> Created at: 2018-03-06 21:08:02 UTC  
> Url: https://github.com/boostorg/geometry/issues/450#issuecomment-370928657  

Yes, it's still open. Why do you ask?

---

## Comment 3

> Username: asrdav  
> Created at: 2018-03-07 10:04:16 UTC  
> Url: https://github.com/boostorg/geometry/issues/450#issuecomment-371087755  

I wanted to contribute to it!  
Can someone please tell me how to install b2 in ubuntu 16.04

---

## Comment 4

> Username: mloskot  
> Created at: 2018-03-07 10:15:05 UTC  
> Url: https://github.com/boostorg/geometry/issues/450#issuecomment-371090874  

> Can someone please tell me how to install b2 in ubuntu 16.04  
  
If you don't know how to get `b2`,you should really read the Wiki first, section "For Contributors"

---

## Comment 5

> Username: asrdav  
> Created at: 2018-03-10 07:56:34 UTC  
> Url: https://github.com/boostorg/geometry/issues/450#issuecomment-372011343  

Thank you for the help.  
I am currently contributing to Boost Libraries for GSOC'18.

---

## Comment 6

> Username: awulkiew  
> Created at: 2019-03-27 17:00:28 UTC  
> Url: https://github.com/boostorg/geometry/issues/450#issuecomment-477255027  

Fixed in https://github.com/boostorg/geometry/pull/567.
