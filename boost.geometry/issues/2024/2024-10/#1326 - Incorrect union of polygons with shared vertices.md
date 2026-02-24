# #1326 - Incorrect union of polygons with shared vertices [Closed]

> Username: lukas-boeger-oxb  
> Created at: 2024-10-08 10:28:45 UTC  
> Updated at: 2024-11-16 10:19:49 UTC  
> Closed at: 2024-11-16 09:18:25 UTC  
> Url: https://github.com/boostorg/geometry/issues/1326  

This issue might be a duplicate of [#1226](https://github.com/boostorg/geometry/issues/1226) or [#1179](https://github.com/boostorg/geometry/issues/1179), and if we can confirm this, happy for this ticket to be closed of course. But the additional example might still be worth filing it. The following two polygons share common vertices:  
  
![image](https://github.com/user-attachments/assets/b47f9286-7a8a-4d98-8f36-f95df450d6bc)  
  
When computing the `union_`, the result is intuitively correct when using Boost 1.77:  
  
![image](https://github.com/user-attachments/assets/eb5f3a1e-39a0-4262-87fe-f2894c2d4710)  
  
With Boost 1.85 or 1.86, this becomes  
  
![image](https://github.com/user-attachments/assets/0266129b-16ef-453f-a73a-73d87214e29a)  
  
The code to reproduce this is as follows (omitting includes etc. for brevity):  
  
```  
namespace bg = boost::geometry;  
using Point = bg::model::point<double, 2, bg::cs::cartesian>;  
  
bg::model::ring<Point> ring_obj = {{-2.89115098058377, -0.25},  
    {-9.34535427093506, -0.25},  
    {-9.34535427093506, 1.75},  
    {-8.74194914027241, 1.75},  
    {-8.75132236364928, 1.78066623424071},  
    {-8.72379640157045, 1.98517649674012},  
    {-8.6971576577442, 2.08892604820013},  
    {-8.46516432879152, 2.10694502899749},  
    {-8.4508627797916, 2.11134809476174},  
    {-8.38919349162909, 2.11284570322019},  
    {-8.12303114735485, 2.13351860011817},  
    {-6.54486857587017, 2.15952214498774},  
    {-6.33512517561985, 2.16974517238479},  
    {-6.09011821087143, 2.17207715057343},  
    {-5.97563428310822, 2.17637335012843},  
    {-5.57133260872244, 2.18630252700583},  
    {-5.07887848661571, 2.18416804606516},  
    {-5.0359022373011, 2.18529905658276},  
    {-1.19310678637991, 2.2589664950919},  
    {-0.345028190203889, 2.01010408632087},  
    {-0.0299072068264135, 1.77159468023247},  
    {-0.02022234569985, 1.75},  
    {-0, 1.75},  
    {-0, 1.7049095146299},  
    {0.246630961988226, 1.15498766340636},  
    {0.268832205492555, 0.460222803594299},  
    {0.180040345346232, 0.355084664215225},  
    {0.180036363994124, 0.284803921497104},  
    {0.086232606470964, 0.174496413890264},  
    {0.0780023740039386, 0.150962829485067},  
    {0.0280089059206103, 0.0970044986024092},  
    {-0, 0.0780684410614274},  
    {-0, -0.25},  
    {-0.452569566324256, -0.25},  
    {-0.959885872828952, -0.575845074192125},  
    {-0.984492757676798, -0.593037244494381},  
    {-0.986053937110457, -0.592652605781279},  
    {-1.03380615828627, -0.623323463099221},  
    {-1.05433104239722, -0.636707963526427},  
    {-1.05455277432398, -0.636648843252325},  
    {-1.08230325522736, -0.654472747969937},  
    {-1.10154815200623, -0.653773778128255},  
    {-1.27688251591406, -0.61035431255586},  
    {-2.30803967424193, -0.423843667604139},  
    {-2.89115098058377, -0.25}};  
  
  
  
bg::model::ring<Point> ring_obs = {{-8.78625207542057, 1.47256621042848},  
    {-8.7713880762347, 1.56833897112359},  
    {-8.54870763952499, 1.98231380430185},  
    {-8.38536606998748, 2.07941797642276},  
    {-8.33146477996655, 2.0901167844798},  
    {-6.33512517561985, 2.16974517238479},  
    {-5.57210759837464, 2.18803744441122},  
    {-1.10154617615509, 2.18080908730283},  
    {-0.182335452668298, 1.77473544450602},  
    {-0.101072642349846, 1.70875977436368},  
    {-0.00289004633636303, 1.59446412805576},  
    {0.123812278264287, 1.4079409294325},  
    {0.130584672613603, 1.37357814410482},  
    {0.17646634130118, 0.897752998467649},  
    {0.184490648597164, 0.600369907975512},  
    {0.180036363994123, 0.284803921497103},  
    {0.0769430724731714, 0.151908777171867},  
    {0.0269420690240159, 0.0979574289336771},  
    {-0.984492757676803, -0.593037244494384},  
    {-1.07964346095732, -0.633124388260264},  
    {-8.50067996780776, 1.25877844922534},  
    {-8.78625207542057, 1.47256621042848}};  
  
std::vector<bg::model::polygon<Point>> output;  
  
bg::union_(ring_obs, ring_obj, output);  
  
```  
I hope this is enough context to make this reproducible. Let me know otherwise, then I will provide more information. Also, I understand that such algorithms don't always have a "right" or "wrong" result, and this might be an unfortunate edge case. Since it's a clear behavioural change compared to v1.77, I would appreciate your opinion on this one.

---

## Comment 1

> Username: barendgehrels  
> Created at: 2024-10-08 16:59:29 UTC  
> Url: https://github.com/boostorg/geometry/issues/1326#issuecomment-2400386947  

Thanks for the report! I can reproduce the issue and will work on a fix, hopefully in November.  
  
Yes, we removed rescaling - so a behaviour change can be there.  
  
For this particular case, if using `float` the (textual) output looks all right. But in general we recommend `double` as you reported.

---

## Comment 2

> Username: barendgehrels  
> Created at: 2024-10-08 17:02:30 UTC  
> Url: https://github.com/boostorg/geometry/issues/1326#issuecomment-2400392441  

Part of textual output: areas of a/b (inputs) and union, which looks like the double of b but that should never happen.  
`a: 16.4881306858 b: 23.1972563788 uni: 56.17724582`  
With float it is:  
`16.4881305541 b: 23.1972562206 uni: 23.200984294`

---

## Comment 3

> Username: lukas-boeger-oxb  
> Created at: 2024-10-08 20:02:30 UTC  
> Url: https://github.com/boostorg/geometry/issues/1326#issuecomment-2400717952  

> I can reproduce the issue and will work on a fix, hopefully in November.  
  
Amazing, thank you!!  
  
> For this particular case, if using float the (textual) output looks all right. But in general we recommend double as you reported.  
  
Indeed, the visualisation confirms this, using 1.85 and `float` instead of `double`:  
  
<img width="626" alt="image" src="https://github.com/user-attachments/assets/19f38a3b-4673-4155-a62b-0312d5e451a5">

---

## Comment 4

> Username: barendgehrels  
> Created at: 2024-11-13 21:42:25 UTC  
> Updated at: 2024-11-13 21:46:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/1326#issuecomment-2474865148  

Root cause: two consecutive turns (4 and 5 in the figure below) are nearly (but not 100% colocated) and have a rare combination of properties, they are classified as touch and touch_interior.  
  
Therefore one points to the next, which is fine, but it causes a travel over the whole polygon to go to the next one. That should be avoided. But in other cases with a touch, it should travel like that (in case of unions).  
  
There is a concept fix but it's not yet in the "ideal" place.  
  
To be continued.  
  
![Image](https://github.com/user-attachments/assets/d2c47a71-ecae-42e3-bae2-717884088b21)

---

## Comment 5

> Username: lukas-boeger-oxb  
> Created at: 2024-11-13 22:03:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/1326#issuecomment-2474916289  

Thanks for the update, much appreciated!

---

## Comment 6

> Username: barendgehrels  
> Created at: 2024-11-14 19:18:23 UTC  
> Url: https://github.com/boostorg/geometry/issues/1326#issuecomment-2477225312  

And the (concept) fix is indeed solving #1226 as well.  
But it does **not** solve #1179   
  
Thanks for the links!

---

## Comment 7

> Username: barendgehrels  
> Created at: 2024-11-16 09:17:44 UTC  
> Url: https://github.com/boostorg/geometry/issues/1326#issuecomment-2480490166  

✅ merged. It might (probably) make it to `1.87`

---

## Comment 8

> Username: lukas-boeger-oxb  
> Created at: 2024-11-16 10:19:48 UTC  
> Url: https://github.com/boostorg/geometry/issues/1326#issuecomment-2480505246  

Amazing!! Thanks a lot!
