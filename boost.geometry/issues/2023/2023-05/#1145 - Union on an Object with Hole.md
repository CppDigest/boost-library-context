# #1145 - Union on an Object with Hole [Closed]

> Username: JohanDore  
> Created at: 2023-05-08 19:52:31 UTC  
> Updated at: 2023-05-17 09:35:54 UTC  
> Closed at: 2023-05-16 12:51:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/1145  

Hi All,  
  
As always, let me start out by giving you a huge thanks for your work on Boost Gometry.  
  
I today attempted an upgrade from version 1.78 to 1.82, but unfortunately, I found a few of our test cases to fail.  
E.g a case where we do a union of a small polygon (**PolyC**) which fits in the hole (**PolyB**) of an outer polygon (**PolyA**):  
![image](https://user-images.githubusercontent.com/9391632/236915800-354004c4-f46d-40e8-b1bf-4036712f1cb9.png)  
I went on tested 1.79, 1.80, & 1.81 as well, and can confirm that I see the same behavior from version 1.79 and above if I do not define BOOST_GEOMETRY_USE_RESCALING before including the geometry headers  
  
Below I tried to make a test case showing the issue as the last two checks fails.  
  
BOOST_AUTO_TEST_CASE(Boost_Union_LostHole)  
{  
	multi_polygon<polygon<point_xy<float>>> polyA;  
	read_wkt("MULTIPOLYGON((("  
		" 7.5000000000000000  4.5000000000000000,"  
		" 2.5000000000000000  4.5000000000000000,"  
		" 0.0000000000000000  0.0000000000000000,"  
		" 0.0000000000000000 10.0000000000000000,"  
		"10.0000000000000000 10.0000000000000000,"  
		"10.0000000000000000  0.0000000000000000,"  
		" 7.5000000000000000  4.5000000000000000)))", polyA);  
  
	multi_polygon<polygon<point_xy<float>>> polyB;  
	read_wkt("MULTIPOLYGON((("  
		" 6.9444446563720703  5.5000000000000000,"  
		" 3.0555555820465088  5.5000000000000000,"  
		" 5.0000000000000000  9.0000000000000000,"  
		" 6.9444446563720703  5.5000000000000000)))", polyB);  
  
	multi_polygon<polygon<point_xy<float>>> polyAB;  
	//Carve hole in PolyA using PolyB  
	difference(polyA, polyB, polyAB);  
  
	//Check that we got one hole with the size of polyB  
	BOOST_CHECK_CLOSE(area(polyAB), area(polyA) - area(polyB), 0.0001);  
	BOOST_CHECK_EQUAL(polyAB.size(), 1u);  
	BOOST_CHECK_EQUAL(polyAB[0].inners().size(), 1u);  
	BOOST_CHECK_EQUAL(-area(polyAB[0].inners()[0]), area(polyB));  
  
	//Poly C is a small bar in the lover part of the hole  
	multi_polygon<polygon<point_xy<float>>> polyC;  
	read_wkt("MULTIPOLYGON((("  
		" 3.3333332538604736  6.0000000000000000,"  
		" 6.6666665077209473  6.0000000000000000,"  
		" 6.9444441795349121  5.5000000000000000,"  
		" 3.0555555820465088  5.5000000000000000,"  
		" 3.3333332538604736  6.0000000000000000)))", polyC);  
  
	//Now make the union of PolyAB & PolyC. And check that it leaves part   
	//of the hole in PolyAB untouched  
	multi_polygon<polygon<point_xy<float>>> polyABC;   
	union_(polyAB, polyC, polyABC);  
	BOOST_CHECK_EQUAL(polyABC.size(), 1u);  
  
	//THESE TWO TEST FAILS - AND I WONDER WHAT'S WRONG  
	BOOST_CHECK_EQUAL(polyABC[0].inners().size(), 1u);  
	BOOST_CHECK_CLOSE(-area(polyABC[0].inners()[0]), area(polyB)-area(polyC), 0.0001);  
}  
  
It can very well be me who has missed something, but wonder if one of you could have a look?  
  
Thanks a lot   
  
Johan

---

## Comment 1

> Username: barendgehrels  
> Created at: 2023-05-10 09:16:06 UTC  
> Url: https://github.com/boostorg/geometry/issues/1145#issuecomment-1541650707  

Hi Johan, thanks for your detailed report. Indeed many things changed since 1.79, and, in most cases, for the good.  
So I will inspect this regression.

---

## Comment 2

> Username: barendgehrels  
> Created at: 2023-05-10 09:50:40 UTC  
> Updated at: 2023-05-10 09:51:38 UTC  
> Url: https://github.com/boostorg/geometry/issues/1145#issuecomment-1541823947  

I can reproduce your problem. Two observations:  
  
- with `double` the test will succeed (I test it in a slightly other way, but probably for you as well)  
- with `float` there were already some artifacts (with the define)  
  
![image](https://github.com/boostorg/geometry/assets/334849/3cd85d7f-9592-40c2-b666-297fed6e10e8)  
  
(this is zoomed in)  
  
Those artifacts indicate a precision or robustness problem, I'll continue (I understand you don't want to switch to double, just for this).

---

## Comment 3

> Username: JohanDore  
> Created at: 2023-05-10 20:21:03 UTC  
> Url: https://github.com/boostorg/geometry/issues/1145#issuecomment-1542761032  

Thanks for the update Barend. You have my mail and number and I wonder if you could give me a call when you have time

---

## Comment 4

> Username: JohanDore  
> Created at: 2023-05-11 19:33:05 UTC  
> Url: https://github.com/boostorg/geometry/issues/1145#issuecomment-1544566610  

I think I'll try to switch to double

---

## Comment 5

> Username: JohanDore  
> Created at: 2023-05-16 12:51:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/1145#issuecomment-1549602072  

Switching to double and using no scaling solves this am many many other problems we have registered over the years.  
Thanks a million

---

## Comment 6

> Username: barendgehrels  
> Created at: 2023-05-17 09:35:54 UTC  
> Url: https://github.com/boostorg/geometry/issues/1145#issuecomment-1551070466  

@JohanDore good to hear!  
Thank you!
