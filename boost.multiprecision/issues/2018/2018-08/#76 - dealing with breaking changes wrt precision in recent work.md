# #76 - dealing with breaking changes wrt precision in recent work [Closed]

> Username: ofloveandhate  
> Created at: 2018-08-19 04:41:57 UTC  
> Updated at: 2018-09-08 07:07:41 UTC  
> Closed at: 2018-09-04 21:06:15 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/76  

i have been writing and testing my library using recent boost versions -- 1.65 and up.  in fixing many issues these past two weeks, i edited much code & many tests related to precision throughout my library to comply with the two precision rules established in #60 (originally about mixed precision):  
  
- Copying or move-assignment copies the precision of the source.  
- Assignment keeps the precision of the target.  
  
with the most recent version of develop, (almost) all my tests pass.  (the failing ones are related to #75).  when compiling against 1.67, many fail, due to precision policy fixes.  
  
i currently need to be able to support users with any version from 1.65 on.  once Boost 1.69 is out with these recent changes, i can enforce a minimum of 1.69 (the min version keeps increasing for me, and that's ok! when i started, the min i needed was 1.55, ha).   but, that's not for a while, since 1.68 was released only a few weeks ago, so like 6 months or so of waiting.  
  
options i've identified:   
  
- i can write some `#if` guards around version-specific things, but the fact of the matter is that behaviour changed, and i have to deal with it.     
- i could use Boost.Multiprecision as a submodule with specific commits that I know work.  
  
❓what's your advice on how to deal with this situation?  
  
---   
  
as an example, the following test passes with a current pull from Boost.Multiprecision's develop branch (c5ff8c9), but parts fail with that which comes with Boost 1.67:  
  
```  
BOOST_AUTO_TEST_CASE(precision_through_arithemetic)  
{  
	mpfr_float.default_precision(50);  
  
	mpfr_float x("0.01234567890123456789012345678901234567890123456789");  
	BOOST_CHECK_EQUAL(x.precision(), 50);  
  
	mpfr_float.default_precision(30);  
	mpfr_float y = pow(x,2);  
	BOOST_CHECK_EQUAL(y.precision(), 30);  // OPEN QUESTION: what should this precision be?  
	  
  
	mpfr_float z = x;  
	mpfr_float q(x);  
	BOOST_CHECK_EQUAL(z.precision(), 50);  // fails in 1.67  
	BOOST_CHECK_EQUAL(q.precision(), 50); // fails in 1.67  
  
	BOOST_CHECK(fabs(z - x) < 1e-50); // fails in 1.67  
  
  
	mpfr_float.default_precision(70);  
  
	z = x;  
  
	BOOST_CHECK_EQUAL(z.precision(),50);  
	BOOST_CHECK(fabs(z - x) < 1e-50);  
  
	y.precision(70);  
	z.precision(30);  
  
	BOOST_CHECK_EQUAL(y.precision(),70);  
	BOOST_CHECK_EQUAL(z.precision(),30);  
	BOOST_CHECK_EQUAL(x.precision(),50);  
  
	y = z*x;  
  
	// y is of precision 70 because it's a pre-existing variable.  
	BOOST_CHECK_EQUAL(y.precision(), 70);  
}  
```  
🎍   
thanks for helping!

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-09-02 17:46:04 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/76#issuecomment-417947233  

Is this fixed by APPoS?

---

## Comment 2

> Username: ofloveandhate  
> Created at: 2018-09-03 14:15:50 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/76#issuecomment-418127955  

On one sense, yes.  But, I guess my question was more centered on advice for how to deal with code that requires a minimum Boost version that doesn't yet exist -- the one that contains the most recent changes, namely the APPoS/APPoT resolution -- but I want to be able to update my develop branch in the meantime with more easily compilable results.  
  
Do you think that relying on Boost.Multiprecision as submodule is a good resolution to that problem?  It decouples the minimum Boost version from the specified Multiprecision version, which I would then have to manually maintain.  Is this a good idea?

---

## Comment 3

> Username: ckormanyos  
> Created at: 2018-09-04 19:14:18 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/76#issuecomment-418485083  

Well I am not an expert, but as far as I know Git allowsyou to check out a submodule that actually tracksa specific tagged commit to the parent repository.  
So you could acquire the master branch of Boostand track the submodule multiprecision to a specificcheckin of the master branch of multiprecision.  
Unknown for me:* Can you set up a submodule to track a specificcheckin of the development branch? I don't know.* How to actually do this? Use a GUI interface?Command line options? If so, which ones?  
  
Does anyone out there have Git experiencein this area?  
With kind regards, Chris  
   On Monday, September 3, 2018, 4:15:52 PM GMT+2, danielle brake <notifications@github.com> wrote:    
   
   
On one sense, yes. But, I guess my question was more centered on advice for how to deal with code that requires a minimum Boost version that doesn't yet exist -- the one that contains the most recent changes, namely the APPoS/APPoT resolution -- but I want to be able to update my develop branch in the meantime with more easily compilable results.  
  
Do you think that relying on Boost.Multiprecision as submodule is a good resolution to that problem? It decouples the minimum Boost version from the specified Multiprecision version, which I would then have to manually maintain. Is this a good idea?  
  
—  
You are receiving this because you are subscribed to this thread.  
Reply to this email directly, view it on GitHub, or mute the thread.

---

## Comment 4

> Username: ofloveandhate  
> Created at: 2018-09-04 21:06:15 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/76#issuecomment-418517902  

yes, i can track specific arbitrary commits.  i already do this with [minieigen](https://github.com/eudoxos/minieigen), which exposes [Eigen](http://eigen.tuxfamily.org/) to Python.  
  
i think i am going to do this.  then, i can use much older versions of Boost, and Multiprecision is guaranteed to be current.  i am closing this issue, i think i got what i needed.  thanks for your input!!!  
  
🙇‍♀️

---

## Comment 5

> Username: jzmaddock  
> Created at: 2018-09-06 18:35:44 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/76#issuecomment-419198014  

>i think i am going to do this. then, i can use much older versions of Boost, and Multiprecision is guaranteed to be current.  
  
Be careful about "much older" - occasionally Multiprecision and Math libraries get coordinated changes - there weren't any in the last release I think, but much before that and strange things may happen!

---

## Comment 6

> Username: ofloveandhate  
> Created at: 2018-09-07 21:37:30 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/76#issuecomment-419572504  

ok, thanks!!!  my current min is 1.65. i'll keep it there til 1.69 comes out.  
  
out of curiosity, are there any checks in Multiprecision/Math on these coordinated changes, to ensure compatibility?  or is the assumption that the versions line up?

---

## Comment 7

> Username: jzmaddock  
> Created at: 2018-09-08 07:07:41 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/76#issuecomment-419619147  

That they line up.  
  
If decoupling/modularisation becomes common practice then I might revisit.
