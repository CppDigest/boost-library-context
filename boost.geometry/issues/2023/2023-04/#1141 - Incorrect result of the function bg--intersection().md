# #1141 - Incorrect result of the function bg::intersection() [Closed]

> Username: linshu77  
> Created at: 2023-04-23 13:49:22 UTC  
> Updated at: 2024-11-16 10:53:10 UTC  
> Closed at: 2024-11-16 10:53:09 UTC  
> Url: https://github.com/boostorg/geometry/issues/1141  

It is compiled by MSVC2017, Boost version: 1.82.  
But the result of the function bg::intersection() was wrong.  
The poly12 obtained in the following code is incorrect.  
```  
void fun()  
{  
	typedef bg::model::d2::point_xy<double> DPoint;  
	typedef bg::model::polygon<DPoint, false> DPolygon;  
	typedef bg::model::multi_polygon<DPolygon> MPolygon;  
	DPolygon poly1;  
	DPolygon poly2;  
	MPolygon poly12, poly21;  
	std::list<DPoint> lstOf = boost::assign::list_of  
	(DPoint(193.57019478631327, 132.59017250377539))  
		(DPoint(207.72957099999999, 125.50837199999999))  
		(DPoint(227.94565200000000, 129.20828800000001))  
		(DPoint(253.72333800000001, 133.70304100000001))  
		(DPoint(273.04274400000003, 142.37692500000000))  
		(DPoint(237.02923500000000, 141.16036299999999))  
		(DPoint(235.71143835000001, 140.78686934999999))  
		(DPoint(235.71143834104575, 140.78686934746216))  
		(DPoint(235.16649600000000, 140.57998100000000))  
		(DPoint(234.62243732380651, 140.42779924762760))  
		(DPoint(221.02097041896951, 136.62325543831733))  
		(DPoint(216.04524400000000, 135.21302100000000))  
		(DPoint(212.46779402999999, 134.76212731000001))  
		(DPoint(212.46779340840936, 134.76212723165614))  
		(DPoint(202.14415900000000, 133.40320600000001))  
		(DPoint(200.49203299713540, 133.25272953098235))  
		(DPoint(200.49203291000001, 133.25272952000000))  
		(DPoint(198.35886600000001, 132.98387000000000))  
		(DPoint(194.25756100000001, 132.65008200000000))  
		(DPoint(193.57019478631327, 132.59017250377539));  
	poly1.outer().assign(lstOf.begin(), lstOf.end());  
	lstOf = boost::assign::list_of  
	(DPoint(230.01056645925340, 165.85545400000001))  
		(DPoint(230.01056645925340, 129.56833895494114))  
		(DPoint(240.65447920262758, 131.42427585847156))  
		(DPoint(240.65447920262758, 165.85545400000001))  
		(DPoint(230.01056645925340, 165.85545400000001));  
	poly2.outer().assign(lstOf.begin(), lstOf.end());  
	bg::correct(poly1);  
	bg::correct(poly2);  
	bg::intersection(poly1, poly2, poly12);  
	double Area1 = bg::area(poly1);  
	double Area2 = bg::area(poly2);  
	double Area12 = bg::area(poly12);  
}  
```

---

## Comment 1

> Username: linshu77  
> Created at: 2023-04-23 14:49:54 UTC  
> Url: https://github.com/boostorg/geometry/issues/1141#issuecomment-1519085043  

poly1 and poly2 :  
![1](https://user-images.githubusercontent.com/7291857/233846654-bf1a45cd-3d01-46a0-aa48-f26363b614c6.png)  
poly12 and poly2 :  
![2](https://user-images.githubusercontent.com/7291857/233846724-5ec007fd-9301-47ca-9fad-4b9f6e642d22.png)

---

## Comment 2

> Username: barendgehrels  
> Created at: 2023-04-23 15:56:50 UTC  
> Url: https://github.com/boostorg/geometry/issues/1141#issuecomment-1519098577  

Thanks for the report. I'll have a look soon.

---

## Comment 3

> Username: linshu77  
> Created at: 2023-04-30 01:46:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/1141#issuecomment-1528916141  

What can I do to avoid this problem now.

---

## Comment 4

> Username: barendgehrels  
> Created at: 2023-04-30 13:35:09 UTC  
> Url: https://github.com/boostorg/geometry/issues/1141#issuecomment-1529027549  

You could try to use the define `BOOST_GEOMETRY_USE_RESCALING`  
This is how it used to work, but it sometimes gave problems.  
Please let me know if this works for you.

---

## Comment 5

> Username: linshu77  
> Created at: 2023-04-30 15:51:11 UTC  
> Updated at: 2023-04-30 15:54:02 UTC  
> Url: https://github.com/boostorg/geometry/issues/1141#issuecomment-1529058476  

if I use the define BOOST_GEOMETRY_USE_RESCALING  
The above code will work.  
But The following code will be incorrect.  
  
```  
typedef bg::model::d2::point_xy<double> DPoint;  
	typedef bg::model::polygon<DPoint, false> DPolygon;  
	typedef bg::model::multi_polygon<DPolygon> MPolygon;  
	DPolygon poly1;  
	DPolygon poly2;  
	MPolygon poly12, poly21;  
	std::list<DPoint> lstOf = boost::assign::list_of  
	(DPoint(171.60480351137778, 162.32096650635697))  
		(DPoint(172.21795926332567, 162.99736073096460))  
		(DPoint(172.21795926332567, 170.00000000000000))  
		(DPoint(171.60480351137778, 170.00000000000000))  
		(DPoint(171.60480351137778, 162.32096650635697));  
	poly1.outer().assign(lstOf.begin(), lstOf.end());  
	lstOf = boost::assign::list_of  
	(DPoint(200.00000000000000, 166.00002000000001))  
		(DPoint(184.00004999999999, 166.00002000000001))  
		(DPoint(180.00004999999999, 164.00002000000001))  
		(DPoint(169.99998889000000, 162.00000277999999))  
		(DPoint(182.00000000000000, 159.00000000000000))  
		(DPoint(200.00000000000000, 159.00000000000000))  
		(DPoint(200.00000000000000, 166.00002000000001));  
	poly2.outer().assign(lstOf.begin(), lstOf.end());  
	bg::correct(poly1);  
	bg::correct(poly2);  
	bg::intersection(poly1, poly2, poly12);  
```

---

## Comment 6

> Username: linshu77  
> Created at: 2023-04-30 15:51:37 UTC  
> Url: https://github.com/boostorg/geometry/issues/1141#issuecomment-1529058551  

![3](https://user-images.githubusercontent.com/7291857/235362905-7800f58a-5f6f-430c-b09e-a733316439af.png)  
![4](https://user-images.githubusercontent.com/7291857/235362910-5c6c1747-3820-4d0a-aee4-e1da240ca863.png)

---

## Comment 7

> Username: crichaud-work  
> Created at: 2023-05-19 05:41:39 UTC  
> Updated at: 2023-05-22 01:51:38 UTC  
> Url: https://github.com/boostorg/geometry/issues/1141#issuecomment-1554034318  

I have the same issue. I tried to use BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE  
@barendgehrels have you been able to determine what was wrong? do you need more feedback?

---

## Comment 8

> Username: isaacVandermeulen  
> Created at: 2023-05-26 22:55:21 UTC  
> Url: https://github.com/boostorg/geometry/issues/1141#issuecomment-1565046110  

I've been having this problem with the polygons:  
```  
std::string wkt_0 = "POLYGON((0.218618541956 0.000301796942949,-0.00384804653004 0.134821310639,-0.0298007167876 1.04400789738,1.45902979374 1.05191230774,1.46124017239 0.00201447494328,0.218618541956 0.000301796942949))";  
std::string wkt_1 = "MULTIPOLYGON(((-0.156520828605 -0.210000038147,-0.210000038147 -0.210000038147,-0.209999978542 2.83500003815,1.05000007153 2.83500003815,1.05000007153 2.94000005722,1.25999999046 2.94000005722,1.25999999046 2.83500003815,1.57500004768 2.83500003815,1.57500004768 2.94000005722,1.78500008583 2.94000005722,1.78500008583 2.83500003815,1.88999998569 2.83500003815,1.88999998569 2.94000005722,2.20500016212 2.94000005722,2.20499992371 2.83500003815,2.41499996185 2.83500003815,2.41499996185 2.94000005722,3.57000017166 2.94000029564,3.57000017166 2.83500003815,3.67500019073 2.83500003815,3.67500042915 1.99500012398,3.78000068665 1.99500024319,3.77999997139 1.88999986649,3.99000024796 1.88999986649,3.99000072479 1.99500024319,3.8850004673 1.99500012398,3.8850004673 2.09999990463,3.78000044823 2.10000014305,3.78000044823 2.3100001812,3.8850004673 2.3100001812,3.8850004673 2.51999998093,3.78000044823 2.51999998093,3.78000020981 3.33812499046,3.90250039101 3.33812499046,3.90250039101 3.68812513351,3.55250024796 3.68812513351,3.55250024796 3.57000041008,3.46500039101 3.57000041008,3.46500015259 3.46500039101,3.55250000954 3.46500039101,3.55250024796 3.33812499046,3.57000017166 3.33812475204,3.57000017166 3.25500011444,3.67500042915 3.25500011444,3.67500042915 3.15000009537,3.57000017166 3.15000033379,3.57000017166 3.04500007629,0.945000052452 3.04500031471,0.945000052452 2.94000029564,0.420000046492 2.94000005722,0.420000076294 3.04500031471,-0.210000023246 3.04500007629,-0.210000067949 4.83000040054,-0.105000019073 4.83000040054,-0.105000004172 4.93500089645,3.14999985695 4.93499994278,3.14999985695 4.83000040054,3.25499987602 4.82999992371,3.25499987602 4.93500041962,3.57000017166 4.93500041962,3.56999993324 4.72500038147,3.67499995232 4.72500038147,3.67499995232 4.6200003624,3.56999993324 4.6200003624,3.57000017166 4.41000032425,3.77999997139 4.41000032425,3.78000068665 4.93500041962,3.99000048637 4.93499994278,3.99000048637 5.03999853134,4.09500026703 5.03999853134,4.09500074387 4.93499994278,4.20000076294 4.93499994278,4.20000076294 5.03999853134,4.30500078201 5.03999853134,4.30500125885 4.93499994278,6.72000026703 4.93500041962,6.72000026703 2.51999998093,6.8250002861 2.52000021935,6.82500076294 2.20499992371,6.72000026703 2.20499992371,6.72000026703 1.25999987125,6.82500076294 1.25999987125,6.82500076294 1.15499997139,6.72000026703 1.15499997139,6.72000026703 1.05000019073,6.8250002861 1.05000007153,6.8250002861 0.945000231266,6.72000026703 0.945000052452,6.72000026703 5.96046447754e-08,6.61500024796 2.98023223877e-08,6.61500024796 0.105000048876,6.2999997139 0.104999989271,6.2999997139 0,6.19500017This happens when the two polygons have overlapping edges,166 -2.98023223877e-08,6.19500017166 0.104999989271,5.7750005722 0.104999989271,5.77500009537 -0.209999918938,5.88000059128 -0.209999918938,5.88000059128 -0.105000019073,6.72000026703 -0.104999959469,6.72000074387 -2.94000005722,3.88500022888 -2.94000029564,3.88500022888 -2.83500027657,3.78000020981 -2.83500027657,3.78000020981 -2.62500023842,3.88500022888 -2.625,3.88500022888 -2.20500016212,3.78000020981 -2.20500040054,3.78000020981 -1.89000034332,3.56999993324 -1.8900001049,3.57000017166 -2.10000038147,3.67500019073 -2.10000014305,3.67500019073 -2.83500027657,3.57000017166 -2.83500027657,3.56999993324 -2.94000029564,0.314999997616 -2.94000029564,0.315000027418 -2.83500027657,0.210000023246 -2.83500003815,0.209999993443 -2.94000029564,-0.105000004172 -2.94000005722,-0.105000004172 -2.83500003815,-0.210000008345 -2.83500003815,-0.210000008345 -0.419999986887,-0.315000027418 -0.420000106096,-0.315000027418 -0.31500005722,0.105000004172 -0.31500005722,0.105000004172 -0.210000038147,-0.0644770413637 -0.210000038147,0.240616455674 -0.0129997208714,-0.163027688861 0.231072902679,-0.156520828605 -0.210000038147)))";  
```  
I noticed that `boost::geometry::intersects()` returns true even though `boost::geometry::intersection()` is empty. So I've gotten around this issue with:  
```  
template <typename T, typename S>  
MultiPoly robust_intersection(const T& poly_0, const S& poly_1)  
{  
    MultiPoly result;  
    boost::geometry::intersection(poly_0, poly_1, result);  
    if (result.size() == 0 && boost::geometry::intersects(poly_0, poly_1)) {  
        float eps = 1e-3;  
        MultiPoly buffered_0;  
        MultiPoly buffered_1;  
        boost::geometry::buffer(poly_0, buffered_0, eps);  
        boost::geometry::buffer(poly_1, buffered_1, eps);  
        MultiPoly buffered_result;  
        boost::geometry::intersection(buffered_0, buffered_1, buffered_result);  
        boost::geometry::buffer(buffered_result, result, -eps);  
    }  
    return result;  
}  
```  
Somehow buffering the inputs by a very small amount is enough that the intersection is computed properly and then buffering by the negative gives what the intersection should have been

---

## Comment 9

> Username: snowman791  
> Created at: 2023-09-13 07:01:19 UTC  
> Url: https://github.com/boostorg/geometry/issues/1141#issuecomment-1717058703  

on version 1.83 I have a similar issue the result polygon is same as polygon Q, but when the polygon P has instead of the value 1e-14  eg. 1e-13 or 1e-15, the result is empty, which is correct.   
  
  using boost_point_2d        = boost::geometry::model::d2::point_xy<double>;  
    using boost_polygon_2d      = boost::geometry::model::polygon<boost_point_2d, false>;  
    using boost_multipolygon_2d = boost::geometry::model::multi_polygon<boost_polygon_2d>;  
  
    boost_polygon_2d P = {{{1e-14, 0}, {-10, 50}, {-20, 50},   {1e-14, 0}}};  
    boost_polygon_2d Q = {{{0, 1000},  {0, 0},   {1000, 0},  {1000, 1000},  {0, 1000}}};  
    boost_multipolygon_2d R;  
      
    bg::intersection(P, Q, R);  
![image](https://github.com/boostorg/geometry/assets/73174595/c9a9d587-ffbd-4556-a5c4-72429b445c79)

---

## Comment 10

> Username: barendgehrels  
> Created at: 2023-09-14 10:11:34 UTC  
> Url: https://github.com/boostorg/geometry/issues/1141#issuecomment-1719165260  

Hi @snowman791 , @isaacVandermeulen , @crichaud-work !  
Please don't report additional problems to this ticket. In general, all reported issues have different root causes. Therefore it is convenient to have different tickets, to be able to track them properly and close the ticket.  
I have to ignore them in this ticket.  
If you have problems with `double` and without `BOOST_GEOMETRY_USE_RESCALING`, you can report them in a new ticket. Thanks.

---

## Comment 11

> Username: barendgehrels  
> Created at: 2023-09-14 10:19:15 UTC  
> Url: https://github.com/boostorg/geometry/issues/1141#issuecomment-1719176728  

I can still reproduce the original problem.

---

## Comment 12

> Username: barendgehrels  
> Created at: 2023-09-14 10:34:05 UTC  
> Url: https://github.com/boostorg/geometry/issues/1141#issuecomment-1719198205  

Detailed explanation of the cause.  
  
The list of ordered turns in `double`:  
```  
0 cl=-1 meth=i seg=s:0, v:9 dst=326.20650572238702125/493.55799435738089187 (0.66092842067549173457) op=iu (i) nxt=-1 / 1 [vx 14]  
1 cl=-1 meth=i seg=s:0, v:14 dst=124.8214289472136187/1239.9875438805390786 (0.10066345388969404062) op=ui (u) nxt=-1 / 3 [vx 16]  
3 cl=-1 meth=i seg=s:0, v:16 dst=860.46806893627024238/935.39785747740393163 (0.91989527456989739207) op=ui (u) nxt=2 / 2 [vx 16]  
2 cl=-1 meth=i seg=s:0, v:16 dst=9.2370555648813024163e-14/9.9475983006414026022e-14 (0.92857142857142860315) op=iu (i) nxt=-1 / 0 [vx 9]  
```  
  
and in `long double`:  
```  
0 cl=-1 meth=i seg=s:0, v:9 dst=326.20650572238700418/493.55799435738086683 (0.66092842067549173457) op=iu (i) nxt=-1 / 1 [vx 14]  
1 cl=-1 meth=i seg=s:0, v:14 dst=124.8214289472136139/1239.9875438805390043 (0.10066345388969404062) op=ui (u) nxt=-1 / 2 [vx 16]  
2 cl=-1 meth=i seg=s:0, v:16 dst=9.0564708510321167978e-14/1.0200867928134016438e-13 (0.88781375416638319553) op=iu (i) nxt=3 / 3 [vx 16]  
3 cl=-1 meth=i seg=s:0, v:16 dst=860.46806893627028162/935.39785747740398342 (0.91989527456989739207) op=ui (u) nxt=-1 / 0 [vx 9]  
```  
  
The `long double` list is correct. In the double version, the `segment_ratio` is `9.2370555648813024163e-14/9.9475983006414026022e-14` and in `long double` it is `9.0564708510321167978e-14/1.0200867928134016438e-13`. Both numerator and denumerator are very small and therefore the result is unstable, and the order is flipped.  
  
This requires rethinking, I cannot fix this issue now.  
With real removing of rescaling, this `segment_ratio` should (I think) change as well, considerably, and be simplified.  
@tinko92 @vissarion @awulkiew any ideas?

---

## Comment 13

> Username: tinko92  
> Created at: 2023-09-14 12:37:44 UTC  
> Updated at: 2023-09-14 12:47:38 UTC  
> Url: https://github.com/boostorg/geometry/issues/1141#issuecomment-1719372393  

I'm still not that familiar with the overlay code. If I understand it correctly, the segment_ratio is computed in cartesian_segments::unified(...) via Cramer's rule and later used for sorting via segment_ratio::operator<(...) either based on the approximation or segment_ratio::less.  
  
I think my approach to guarantee correct (and thereby consistent) behaviour for all cases would be as follows:  
* At the initial segment_ratio computation, we need to  
    * Compute rigorous error bounds for the segment_ratio approximation and store them in some `double err` such that the true ratio (which may not be representable in double) is within [approximation - err, approximation + err].  
    * Store some reference or point to the original segments.  
* In the comparison, we need to  
    * Replace close_to by checking whether the err-radius intervals around the segment_ratio approximation overlap. They should only do this in degenerate cases. If they do not, the comparison of the approximations is sufficient.  
    * If they do, the ratio inequality with the original points can be rearranged such that all fractions disappear and evaluated in a numerically robust manner. This doesn't need to be done with full precision right away; the precision can be increased incrementally as needed, and there are probably some shortcuts to check.  
  
This would require some time to implement. I cannot do it right now.  
  
I expect no performance penalty for the general case, possibly even a speedup if these code paths are critical and the number of branches can be reduced. In degenerate cases, there could be a performance penalty for inputs that require the evaluation of all digits, but this penalty would only apply in cases that might currently be computed incorrectly.  
  
The behaviour would be correct with respect to the input coordinates, but I lack the general view of the overlay implementation that would be required to rule out new inconsistencies that might arise between such exact segment_ratio comparisons and other parts of the algorithm that still deliberately use tolerances like, e.g. the computation of clusters.

---

## Comment 14

> Username: barendgehrels  
> Created at: 2023-09-14 16:44:26 UTC  
> Url: https://github.com/boostorg/geometry/issues/1141#issuecomment-1719795271  

Hi @tinko92 , thanks for your quick and thorough answer!  
  
Looks like great ideas. I think there is no hurry, it is a rare case, already open for nearly 5 months, and it is convenient if we first remove rescaling (planned for this quarter, I think). And then we can use your ideas. The turns contain indices to the original segments, so where they are used, or compared, that should be available.  
  
I'm not on github coming weeks so don't expect much answer from my side.

---

## Comment 15

> Username: crichaud-work  
> Created at: 2023-09-15 04:49:41 UTC  
> Updated at: 2023-09-15 05:12:44 UTC  
> Url: https://github.com/boostorg/geometry/issues/1141#issuecomment-1720644858  

@barendgehrels I have an issue with bg::intersection(). BOOST_GEOMETRY_USE_RESCALING is deprecated and we are supposed to use BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE which is underneath BOOST_GEOMETRY_USE_RESCALING. so yes I can still reproduce the original issue. We upgraded boost from 1.62 to 1.83 and this is where the problem started. We have unit tests and system tests for testing intersection.

---

## Comment 16

> Username: barendgehrels  
> Created at: 2023-09-15 10:57:47 UTC  
> Url: https://github.com/boostorg/geometry/issues/1141#issuecomment-1721079177  

Hi @crichaud-work , can you please open a new issue with the exact reproduction scenario (eg on godbolt).  
Yes, both `BOOST_GEOMETRY_USE_RESCALING` and `BOOST_GEOMETRY_ROBUSTNESS_ALTERNATIVE` (the same now indeed) are deprecated indeed.

---

## Comment 17

> Username: barendgehrels  
> Created at: 2024-11-16 10:53:09 UTC  
> Url: https://github.com/boostorg/geometry/issues/1141#issuecomment-2480521755  

Current main branch gives correct behavior.  
Was fixed before `2024-02-12`
