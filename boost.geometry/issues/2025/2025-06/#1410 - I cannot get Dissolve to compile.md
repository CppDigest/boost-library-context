# #1410 - I cannot get Dissolve to compile [Open]

> Username: JohanDore  
> Created at: 2025-06-22 19:30:40 UTC  
> Updated at: 2025-08-04 05:57:12 UTC  
> Url: https://github.com/boostorg/geometry/issues/1410  

Hi All,  
  
As always, I want to start by thanking you for your work on Boost Geometry. It's been incredibly helpful to us.  
  
We still use Dissolve to fix erroneous polygons that are drawn manually or received from third parties. We encounter various errors, and Dissolve has been a great function for us over the years.   
  
If there are any alternatives, I would be happy to learn how to avoid using Dissolve. But if not, please help get Dissolve to compile again? It worked in 1.85 but with 1.88 I've been trying for the past few hours without any progress, so I probably can't make it work in finite time  
  
Thanks a lot.  
  
Best regards,   
  
Johan

---

## Comment 1

> Username: barendgehrels  
> Created at: 2025-06-22 20:57:16 UTC  
> Url: https://github.com/boostorg/geometry/issues/1410#issuecomment-2994446136  

hi @JohanDore ,  
  
Thanks for reporting this issue!  
  
Last spring I have done a big improvement in handling all intersection/union/difference code. I had also dissolve in mind - it should be easier now - but did not yet get to that part yet.  
  
But I will do now, I hope to be able to work on it this and next Month - and it would actually be great to finally have it in the normal branch as well.  
  
Best regards, Barend

---

## Comment 2

> Username: JohanDore  
> Created at: 2025-06-23 06:29:33 UTC  
> Url: https://github.com/boostorg/geometry/issues/1410#issuecomment-2995097362  

Hi @barendgehrels,  
  
Oh that is great to hear. Let me know if there is anything I can do to help  
  
Best regards  
  
Johan

---

## Comment 3

> Username: barendgehrels  
> Created at: 2025-06-25 17:06:48 UTC  
> Updated at: 2025-06-25 17:07:12 UTC  
> Url: https://github.com/boostorg/geometry/issues/1410#issuecomment-3005521066  

hi @JohanDore   
  
First step is there #1411 . See comments there. It compiles again - but it works not yet **for all cases.** (It works for some cases though)  
  
However, if you want to target `1.88` then most of the changes would do for you. What is not working yet is the adaptation to the new underlying Graph algorithm. Hope to continue that next week.

---

## Comment 4

> Username: JohanDore  
> Created at: 2025-06-29 18:38:12 UTC  
> Updated at: 2025-06-30 19:58:53 UTC  
> Url: https://github.com/boostorg/geometry/issues/1410#issuecomment-3016965146  

Oh thanks a lot @barendgehrels. It's always a pleasure working with you:-)   
I can confirm that it compiles under 1.88, however quite a few of my dissolve unitest still fails, e.g:  
  
	polygon<DPointXY> Poly;  
	read_wkt("POLYGON((0 0, 0 4, 4 4, 4 0, 1 0, 1 3, 3 3, 3 0))", Poly);  
	correct(Poly);  
  
	DMultiPolygon Dissolved;  
	boost::geometry::dissolve(Poly, Dissolved);  
  
	BOOST_CHECK(is_valid(Dissolved));  
	BOOST_CHECK_EQUAL(Dissolved.size(), 1);  
	BOOST_CHECK_EQUAL(Dissolved[0].outer().size(), 5); 	//It should have 5 points  
	BOOST_CHECK_EQUAL(Dissolved[0].inners().size(), 0);  
	BOOST_CHECK_CLOSE(area(Dissolved), 16.0, 0.0001);  
  
worked under 1.85.  
  
So maybe I better await you next iterations

---

## Comment 5

> Username: barendgehrels  
> Created at: 2025-07-05 09:33:27 UTC  
> Url: https://github.com/boostorg/geometry/issues/1410#issuecomment-3038612267  

> Oh thanks a lot [@barendgehrels](https://github.com/barendgehrels). It's always a pleasure working with you:-)   
  
😎 thanks! Likewise!  
  
> I can confirm that it compiles under 1.88  
  
Good to hear.   
  
> however quite a few of my dissolve unitest still fails, e.g:  
  
Yes it is not yet the same. And in `1.89` it will be different again, because of changes in the underlying algorithm. Thanks for your test, I will add it.  
  
  
> So maybe I better await you next iterations  
  
Hope to improve things!

---

## Comment 6

> Username: barendgehrels  
> Created at: 2025-07-05 10:43:57 UTC  
> Url: https://github.com/boostorg/geometry/issues/1410#issuecomment-3038744713  

> Thanks for your test, I will add it.  
  
I had it already, it's `dissolve_mail_2017_10_26_b` ;-)

---

## Comment 7

> Username: barendgehrels  
> Created at: 2025-07-19 12:45:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/1410#issuecomment-3092332912  

hi @JohanDore the next step is done. Actually there are 3 alternatives now:  
  
* the original, which works now reasonable (better than the version 2 weeks ago)  
* a version using `buffer` which works fine BUT:  
   * it removes all interior rings  
   * it can change the coordinates a bit  
* a version which was submitted in 2021 already - and discussed - but never made it into the library. This is now called `dissolve_using_correct`.  
  
If you have the time, is it possible to judge what is the best option for you?

---

## Comment 8

> Username: barendgehrels  
> Created at: 2025-07-19 12:47:01 UTC  
> Url: https://github.com/boostorg/geometry/issues/1410#issuecomment-3092333762  

Few more remarks:  
  
* `TEST_DISSOLVE(dissolve_mail_2017_10_26_b, 16.0, 1, 0, 5);` should be fixed now (the case you described above)  
* the alternative using buffer needs the `develop` version of Boost.Geometry, because it uses the `convert` which I extended (w.r.t. geometry types) for this reason

---

## Comment 9

> Username: JohanDore  
> Created at: 2025-07-19 15:40:52 UTC  
> Url: https://github.com/boostorg/geometry/issues/1410#issuecomment-3092417140  

Thank a lot @barendgehrels   
  
I'll give it a try when I am back in afew days and let you know

---

## Comment 10

> Username: JohanDore  
> Created at: 2025-08-02 07:20:43 UTC  
> Url: https://github.com/boostorg/geometry/issues/1410#issuecomment-3146287534  

Hi Barend,  
  
If I could hand out a Nobel Prize for contributions to Boost.Geometry, I’d be standing on stage in Stockholm on December 10th with your name on the envelope! 😊  
  
All tests are now passing — even some of the older ones that were previously failing are now working.  
  
Thanks so much to you and everyone contributing to Boost.Geometry.  
  
Best regards,  
  
Johan

---

## Comment 11

> Username: barendgehrels  
> Created at: 2025-08-04 05:57:12 UTC  
> Url: https://github.com/boostorg/geometry/issues/1410#issuecomment-3149223052  

> If I could hand out a Nobel Prize for contributions to Boost.Geometry, I’d be standing on stage in Stockholm on December 10th with your name on the envelope! 😊  
>   
> All tests are now passing — even some of the older ones that were previously failing are now working.  
  
Thanks Johan for your nice comment! But also for your report and helping me finding the root cause, I'm glad that we (most probably) still get it in `1.89` in time.
