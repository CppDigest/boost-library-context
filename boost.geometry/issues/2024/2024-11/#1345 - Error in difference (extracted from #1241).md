# #1345 - Error in difference (extracted from #1241) [Closed]

> Username: barendgehrels  
> Created at: 2024-11-20 15:19:18 UTC  
> Updated at: 2024-11-25 13:12:18 UTC  
> Closed at: 2024-11-25 13:12:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/1345  

(extracted from #1241)  
  
> Hi All,  
>   
>   
>   
> I believe I hit this same issue and thought I may help by creating a few test cases showing to help:  
>   
>   
>   
> ```  
>   
> BOOST_AUTO_TEST_CASE(Boost_Diffence_ClearAll)  
>   
> {  
>   
> 	using PointXY = boost::geometry::model::d2::point_xy<double>;  
>   
> 	using Polygon = boost::geometry::model::polygon<PointXY>;  
>   
> 	using MultiPolygon = boost::geometry::model::multi_polygon<Polygon>;  
>   
>   
>   
> 	MultiPolygon polyA;  
>   
> 	read_wkt("MULTIPOLYGON((("  
>   
> 	    "-2.0785311235613415 -0.6304193410175202,"  
>   
>       "-2.0534946127981359 -0.6304193410175202,"  
>   
>       "-2.0534946127981359 -0.8867932112327471,"  
>   
>       "-2.3098684830133629 -0.8867932112327471,"  
>   
>       "-2.3098684830133629 -0.6554558517807265,"  
>   
>       "-2.2848319722501573 -0.6554558517807265,"  
>   
>       "-2.0785311235613415 -0.6554558517807265,"  
>   
>       "-2.0785311235613415 -0.6304193410175202)))", polyA);  
>   
>   
>   
> 	MultiPolygon polyB;  
>   
> 	read_wkt("MULTIPOLYGON((("  
>   
> 			"-2.0785311235613420 -0.6304193410175202,"  
>   
> 			"-2.0534946127981359 -0.6304193410175202,"  
>   
> 			"-2.0534946127981359 -0.6554558517807265,"  
>   
> 			"-2.0785311235613420 -0.6554558517807265,"  
>   
> 			"-2.0785311235613420 -0.6304193410175202)))", polyB);  
>   
>   
>   
> 	MultiPolygon polyAB;  
>   
> 	//Clip off the small polyB appendix top/left in part polyA  
>   
> 	difference(polyA, polyB, polyAB);  
>   
>   
>   
> 	//Without an error we should get:  
>   
> 	//BOOST_CHECK_CLOSE(area(polyAB), area(polyA) - area(polyB), 0.0001);  
>   
> 	//BOOST_CHECK_EQUAL(polyAB.size(), 1u);  
>   
> 	//BOOST_CHECK(polyAB[0].inners().empty());  
>   
>   
>   
> 	//But we get this:  
>   
> 	BOOST_CHECK(polyAB.empty());  
>   
> }  
>   
>   
>   
> BOOST_AUTO_TEST_CASE(Boost_Diffence_ClearsTooMuch)  
>   
> {  
>   
> 	using PointXY = boost::geometry::model::d2::point_xy<double>;  
>   
> 	using Polygon = boost::geometry::model::polygon<PointXY>;  
>   
> 	using MultiPolygon = boost::geometry::model::multi_polygon<Polygon>;  
>   
>   
>   
> 	MultiPolygon polyA;  
>   
> 	read_wkt("MULTIPOLYGON((("  
>   
> 		"-6.1723606999999996 3.2834021000000000,"  
>   
> 		"-6.1723606999999996 2.8006724999999992,"  
>   
> 		"-5.7133718999999994 2.8006724999999992,"  
>   
> 		"-5.7133718999999994 3.2834021000000000,"  
>   
> 		"-5.6896310999999997 3.2834021000000000,"  
>   
> 		"-5.6896310999999997 2.7769316999999996,"  
>   
> 		"-6.1961014999999993 2.7769316999999996,"  
>   
> 		"-6.1961014999999993 3.2834021000000000,"  
>   
> 		"-6.1723606999999996 3.2834021000000000)))", polyA);  
>   
>   
>   
> 	MultiPolygon polyB;  
>   
> 	read_wkt("MULTIPOLYGON((("  
>   
> 		"-6.1723606999999996 2.8006724999999997,"  
>   
> 		"-5.7133718999999994 2.8006724999999997,"  
>   
> 		"-5.7133718999999994 2.7769316999999996,"  
>   
> 		"-6.1723606999999996 2.7769316999999996,"  
>   
> 		"-6.1723606999999996 2.8006724999999997)))", polyB);  
>   
>   
>   
> 	MultiPolygon polyAB;  
>   
> 	//Clip off the small polyB at the bottom center part of polyA  
>   
> 	difference(polyA, polyB, polyAB);  
>   
>   
>   
> 	//Without an error we should get:  
>   
> 	//BOOST_CHECK_CLOSE(area(polyAB), area(polyA) - area(polyB), 0.0001);  
>   
> 	//BOOST_CHECK_EQUAL(polyAB.size(), 2u);  
>   
> 	//BOOST_CHECK(polyAB[0].inners().empty());  
>   
> 	//BOOST_CHECK(polyAB[1].inners().empty());  
>   
>   
>   
> 	//But we get this:  
>   
> 	BOOST_CHECK_CLOSE(area(polyAB), 0.5*(area(polyA) - area(polyB)), 0.0001);  
>   
> 	BOOST_CHECK(polyAB.size() == 1u);  
>   
> 	BOOST_CHECK(polyAB[0].inners().empty());  
>   
> }  
>   
> ```   
  
 _Originally posted by @JohanDore in [#1241](https://github.com/boostorg/geometry/issues/1241#issuecomment-2183964934)_
