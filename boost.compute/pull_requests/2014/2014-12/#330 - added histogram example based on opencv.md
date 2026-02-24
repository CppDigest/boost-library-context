# #330 added histogram example based on opencv [Merged]

> Username: Mageswaran1989  
> Created at: 2014-12-04 20:45:20 UTC  
> Updated at: 2014-12-11 11:07:42 UTC  
> Merged at: 2014-12-11 04:12:11 UTC  
> Closed at: 2014-12-11 04:12:11 UTC  
> Url: https://github.com/boostorg/compute/pull/330  

Task reference #222  
  
I am not able to have a consistent results with following code.  
Can you have a look on this?

---

## Comment 1

> Username: Mageswaran1989  
> Created_at: 2014-12-04 20:45:57 UTC  
> Updated_at: 2014-12-10 17:14:25 UTC  
> Url: https://github.com/boostorg/compute/pull/330#discussion_r21334460  

Do we need to go with this method with local histogram?

---

## Comment 2

> Username: Mageswaran1989  
> Created_at: 2014-12-04 20:47:05 UTC  
> Updated_at: 2014-12-10 17:14:25 UTC  
> Url: https://github.com/boostorg/compute/pull/330#discussion_r21334546  

i doubt on this direct approach!  
  
In continuous mode values are keep increasing.

---

## Comment 3

> Username: Mageswaran1989  
> Created_at: 2014-12-04 20:47:58 UTC  
> Updated_at: 2014-12-10 17:14:25 UTC  
> Url: https://github.com/boostorg/compute/pull/330#discussion_r21334603  

added to test values, which is always different between CV & CL implementation

---

## Comment 4

> Username: Mageswaran1989  
> Created_at: 2014-12-04 20:56:45 UTC  
> Updated_at: 2014-12-10 17:14:25 UTC  
> Url: https://github.com/boostorg/compute/pull/330#discussion_r21335135  

I tried by adding  
        case CV_32FC4:  
            return image_format(CL_BGRA, CL_FLOAT);  
in /include/boost/compute/interop/opencv/core.hpp  
but didn't help :(

---

## Comment 5

> Username: coveralls  
> Created_at: 2014-12-04 21:02:30 UTC  
> Url: https://github.com/boostorg/compute/pull/330#issuecomment-65703086  

[![Coverage Status](https://coveralls.io/builds/1565286/badge)](https://coveralls.io/builds/1565286)  
  
Changes Unknown when pulling **86e8ca2e4eee2e190aca4636e5b47caa1043eccd on Mageswaran1989:histogram_example** into *\* on kylelutz:develop**.

---

## Comment 6

> Username: kylelutz  
> Created_at: 2014-12-05 05:47:29 UTC  
> Updated_at: 2014-12-10 17:14:25 UTC  
> Url: https://github.com/boostorg/compute/pull/330#discussion_r21356516  

Looks good to me.

---

## Comment 7

> Username: kylelutz  
> Created_at: 2014-12-05 05:48:24 UTC  
> Updated_at: 2014-12-10 17:14:25 UTC  
> Url: https://github.com/boostorg/compute/pull/330#discussion_r21356550  

Interesting, how different? Are you getting consistent results with the OpenCL implementation every time?

---

## Comment 8

> Username: kylelutz  
> Created_at: 2014-12-05 05:49:03 UTC  
> Updated_at: 2014-12-10 17:14:25 UTC  
> Url: https://github.com/boostorg/compute/pull/330#discussion_r21356568  

Are you zeroing out the histograms between each kernel launch?

---

## Comment 9

> Username: kylelutz  
> Created_at: 2014-12-05 05:51:21 UTC  
> Updated_at: 2014-12-10 17:14:25 UTC  
> Url: https://github.com/boostorg/compute/pull/330#discussion_r21356635  

Have you tried with CV_8UC4 (8-bit BGRA)?

---

## Comment 10

> Username: kylelutz  
> Created_at: 2014-12-05 05:53:42 UTC  
> Updated_at: 2014-12-10 17:14:25 UTC  
> Url: https://github.com/boostorg/compute/pull/330#discussion_r21356702  

Hmm, I'd say just keep it simple for now and only implement this example for a single image loaded from disk (like the `qimage_blur` example does). We could always extend it later to also work with streaming images from a camera.

---

## Comment 11

> Username: kylelutz  
> Created_at: 2014-12-05 05:55:33 UTC  
> Updated_at: 2014-12-10 17:14:25 UTC  
> Url: https://github.com/boostorg/compute/pull/330#discussion_r21356738  

This could all probably be on one line and save some vertical space.

---

## Comment 12

> Username: kylelutz  
> Created_at: 2014-12-05 05:55:51 UTC  
> Updated_at: 2014-12-10 17:14:25 UTC  
> Url: https://github.com/boostorg/compute/pull/330#discussion_r21356741  

Do these need to be `volatile`?

---

## Comment 13

> Username: kylelutz  
> Created_at: 2014-12-05 05:57:05 UTC  
> Updated_at: 2014-12-10 17:14:25 UTC  
> Url: https://github.com/boostorg/compute/pull/330#discussion_r21356760  

In the final example I'd remove this function and just demonstrate using OpenCL. This is good for developing and testing though.

---

## Comment 14

> Username: kylelutz  
> Created_at: 2014-12-05 05:58:14 UTC  
> Updated_at: 2014-12-10 17:14:25 UTC  
> Url: https://github.com/boostorg/compute/pull/330#discussion_r21356799  

Can you use `std::vector<int>` instead of doing manual memory allocations? This will also take care of initializing the values so you can remove the calls to `memset()`.

---

## Comment 15

> Username: kylelutz  
> Created_at: 2014-12-05 05:59:10 UTC  
> Updated_at: 2014-12-10 17:14:25 UTC  
> Url: https://github.com/boostorg/compute/pull/330#discussion_r21356827  

You could use `compute::vector<int>` here instead of raw `buffer` objects.

---

## Comment 16

> Username: kylelutz  
> Created_at: 2014-12-05 06:00:29 UTC  
> Url: https://github.com/boostorg/compute/pull/330#issuecomment-65750950  

Looking good. I left a few inline comments/suggestions.

---

## Comment 17

> Username: Mageswaran1989  
> Created_at: 2014-12-05 13:12:46 UTC  
> Updated_at: 2014-12-10 17:14:25 UTC  
> Url: https://github.com/boostorg/compute/pull/330#discussion_r21370967  

Yes I guess, I will test it once again and attach the results.

---

## Comment 18

> Username: Mageswaran1989  
> Created_at: 2014-12-05 13:13:31 UTC  
> Updated_at: 2014-12-10 17:14:25 UTC  
> Url: https://github.com/boostorg/compute/pull/330#discussion_r21370996  

Point taken.

---

## Comment 19

> Username: Mageswaran1989  
> Created_at: 2014-12-05 13:15:14 UTC  
> Updated_at: 2014-12-10 17:14:25 UTC  
> Url: https://github.com/boostorg/compute/pull/330#discussion_r21371048  

By default it uses  
https://github.com/kylelutz/compute/blob/master/include/boost/compute/interop/opencv/core.hpp#L77

---

## Comment 20

> Username: Mageswaran1989  
> Created_at: 2014-12-05 13:16:55 UTC  
> Updated_at: 2014-12-10 17:14:25 UTC  
> Url: https://github.com/boostorg/compute/pull/330#discussion_r21371101  

Okay I will use it, I though this could give a native feel.

---

## Comment 21

> Username: Mageswaran1989  
> Created_at: 2014-12-05 13:17:57 UTC  
> Updated_at: 2014-12-10 17:14:25 UTC  
> Url: https://github.com/boostorg/compute/pull/330#discussion_r21371131  

In that case it is done I guess, graph looks almost the same, except the validation with the OpenCV values.

---

## Comment 22

> Username: Mageswaran1989  
> Created_at: 2014-12-05 13:19:11 UTC  
> Updated_at: 2014-12-10 17:14:25 UTC  
> Url: https://github.com/boostorg/compute/pull/330#discussion_r21371174  

With memset I doing it.

---

## Comment 23

> Username: Mageswaran1989  
> Created_at: 2014-12-05 13:20:25 UTC  
> Updated_at: 2014-12-10 17:14:25 UTC  
> Url: https://github.com/boostorg/compute/pull/330#discussion_r21371204  

I tested with this thinking it might help

---

## Comment 24

> Username: Mageswaran1989  
> Created_at: 2014-12-05 13:22:08 UTC  
> Updated_at: 2014-12-10 17:14:25 UTC  
> Url: https://github.com/boostorg/compute/pull/330#discussion_r21371261  

I hope there are no potential logical problem/bug in this kernel.   
Should we go with the approach pointed in the above link?

---

## Comment 25

> Username: Mageswaran1989  
> Created_at: 2014-12-05 13:22:21 UTC  
> Updated_at: 2014-12-10 17:14:25 UTC  
> Url: https://github.com/boostorg/compute/pull/330#discussion_r21371267  

Point taken.

---

## Comment 26

> Username: kylelutz  
> Created_at: 2014-12-05 16:48:19 UTC  
> Updated_at: 2014-12-10 17:14:25 UTC  
> Url: https://github.com/boostorg/compute/pull/330#discussion_r21383332  

It seems fine to me. Have you noticed any problems when implementing it?

---

## Comment 27

> Username: kylelutz  
> Created_at: 2014-12-05 16:49:44 UTC  
> Updated_at: 2014-12-10 17:14:25 UTC  
> Url: https://github.com/boostorg/compute/pull/330#discussion_r21383419  

I mean, why convert it to floating point (CV_32FC4) instead of just using 8-bit bgra (CV_8UC4)?

---

## Comment 28

> Username: Mageswaran1989  
> Created_at: 2014-12-06 18:06:51 UTC  
> Url: https://github.com/boostorg/compute/pull/330#issuecomment-65907659  

Here is print log for single image:  
mageswaran@OABLN192:/opt/OpenCL/compute/mdbuild$ example/opencv_histogram --image /opt/opencv-3.0.0-alpha/samples/cpp/lena.jpg  
showHistogramWindow called from CV  
[0; 0; 0; 0; 0; 0; 0; 0; 0; 0; 0; 0; 0; 0; 0; 0; 0; 0; 0; 0; 0; 0; 0; 0; 0; 0; 0; 1; 0; 0; 0; 1; 1; 0; 3; 2; 6; 8; 2; 12; 16; 34; 42; 33; 64; 74; 123; 148; 229; 279; 333; 452; 573; 740; 938; 1137; 1294; 1616; 1779; 2091; 2260; 2464; 2684; 2690; 2732; 2793; 2807; 2763; 2782; 2741; 2610; 2649; 2710; 2839; 2981; 2908; 3101; 3091; 3102; 3148; 3026; 2967; 3032; 2851; 2872; 2776; 2783; 2818; 2831; 2970; 2929; 2959; 3217; 3209; 3132; 3260; 3253; 3117; 2999; 2868; 2785; 2655; 2628; 2558; 2620; 2613; 2614; 2746; 2775; 2751; 2661; 2641; 2617; 2591; 2563; 2571; 2601; 2792; 2829; 2862; 3042; 3190; 3250; 3225; 3190; 2933; 2740; 2422; 2197; 1949; 1754; 1489; 1302; 1116; 1045; 968; 848; 863; 863; 883; 878; 837; 848; 862; 846; 786; 798; 801; 888; 892; 868; 906; 835; 858; 964; 1018; 976; 1019; 972; 956; 885; 965; 948; 929; 919; 821; 856; 838; 777; 755; 779; 741; 719; 698; 618; 581; 619; 585; 580; 583; 569; 617; 584; 621; 620; 625; 569; 548; 460; 401; 380; 359; 324; 267; 200; 201; 134; 138; 130; 125; 118; 99; 115; 82; 57; 58; 51; 45; 34; 27; 27; 21; 11; 6; 5; 2; 6; 2; 1; 0; 2; 1; 1; 0; 0; 0; 0; 0; 0; 0; 0; 0; 0; 0; 0; 0; 0; 0; 0; 0; 0; 0; 0; 0; 0; 0; 0; 0; 0; 0; 0; 0; 0; 0; 0; 0]  
showHistogramWindow called from CL  
[-1; -1; -1; -1; -1; -1; -1; -1; -1; -1; -1; -1; -1; -1; -1; -1; -1; -1; -1; -1; -1; -1; -1; -1; -1; -1; -1; 0; -1; -1; -1; 0; 0; -1; 2; 1; 5; 7; 1; 11; 15; 33; 41; 32; 63; 73; 122; 147; 228; 278; 332; 451; 572; 739; 937; 1136; 1293; 1615; 1778; 2090; 2259; 2463; 2683; 2689; 2731; 2792; 2806; 2762; 2781; 2740; 2609; 2648; 2709; 2838; 2980; 2907; 3100; 3090; 3101; 3147; 3025; 2966; 3031; 2850; 2871; 2775; 2782; 2817; 2830; 2969; 2928; 2958; 3216; 3208; 3131; 3259; 3252; 3116; 2998; 2867; 2784; 2654; 2627; 2557; 2619; 2612; 2613; 2745; 2774; 2750; 2660; 2640; 2616; 2590; 2562; 2570; 2600; 2791; 2828; 2861; 3041; 3189; 3249; 3224; 3189; 2932; 2739; 2421; 2196; 1948; 1753; 1488; 1301; 1115; 1044; 967; 847; 862; 862; 882; 877; 836; 847; 861; 845; 785; 797; 800; 887; 891; 867; 905; 834; 857; 963; 1017; 975; 1018; 971; 955; 884; 964; 947; 928; 918; 820; 855; 837; 776; 754; 778; 740; 718; 697; 617; 580; 618; 584; 579; 582; 568; 616; 583; 620; 619; 624; 568; 547; 459; 400; 379; 358; 323; 266; 199; 200; 133; 137; 129; 124; 117; 98; 114; 81; 56; 57; 50; 44; 33; 26; 26; 20; 10; 5; 4; 1; 5; 1; 0; -1; 1; 0; 0; -1; -1; -1; -1; -1; -1; -1; -1; -1; -1; -1; -1; -1; -1; -1; -1; -1; -1; -1; -1; -1; -1; -1; -1; -1; -1; -1; -1; -1; -1; -1; -1; -1]

---

## Comment 29

> Username: Mageswaran1989  
> Created_at: 2014-12-06 19:35:03 UTC  
> Updated_at: 2014-12-10 17:14:25 UTC  
> Url: https://github.com/boostorg/compute/pull/330#discussion_r21418510  

is this changes okay with this pull?

---

## Comment 30

> Username: Mageswaran1989  
> Created_at: 2014-12-06 19:36:46 UTC  
> Updated_at: 2014-12-10 17:14:25 UTC  
> Url: https://github.com/boostorg/compute/pull/330#discussion_r21418521  

is this type a valid one?   
For float type CV images this makes sense

---

## Comment 31

> Username: coveralls  
> Created_at: 2014-12-06 19:44:46 UTC  
> Url: https://github.com/boostorg/compute/pull/330#issuecomment-65910927  

[![Coverage Status](https://coveralls.io/builds/1573604/badge)](https://coveralls.io/builds/1573604)  
  
Changes Unknown when pulling **5f3881bc7d9aa04c67308337df96d76d1dc0180b on Mageswaran1989:histogram_example** into *\* on kylelutz:develop**.

---

## Comment 32

> Username: coveralls  
> Created_at: 2014-12-06 19:48:20 UTC  
> Url: https://github.com/boostorg/compute/pull/330#issuecomment-65911041  

[![Coverage Status](https://coveralls.io/builds/1573608/badge)](https://coveralls.io/builds/1573608)  
  
Changes Unknown when pulling **5f3881bc7d9aa04c67308337df96d76d1dc0180b on Mageswaran1989:histogram_example** into *\* on kylelutz:develop**.

---

## Comment 33

> Username: kylelutz  
> Created_at: 2014-12-06 19:53:49 UTC  
> Updated_at: 2014-12-10 17:14:25 UTC  
> Url: https://github.com/boostorg/compute/pull/330#discussion_r21418659  

I'd do it in a separate branch/pull-request as it doesn't really go with the rest of these changes.

---

## Comment 34

> Username: kylelutz  
> Created_at: 2014-12-06 19:58:04 UTC  
> Updated_at: 2014-12-10 17:14:25 UTC  
> Url: https://github.com/boostorg/compute/pull/330#discussion_r21418690  

Yeah, this looks correct. If you have extra time it would be good to add a test for this format (in `test/test_interop_opencv.cpp`). Something simple like creating small (e.g. 2x2) `cv::Mat` with CV_32FC4, copying it to a `image2d` on the device, then copying it back and verifying the image data remains the same.

---

## Comment 35

> Username: kylelutz  
> Created_at: 2014-12-06 19:59:35 UTC  
> Updated_at: 2014-12-10 17:14:25 UTC  
> Url: https://github.com/boostorg/compute/pull/330#discussion_r21418700  

I think it would also be good to do an `imshow("image", src)` to display the source image side-by-side with the histogram.

---

## Comment 36

> Username: kylelutz  
> Created_at: 2014-12-06 20:00:03 UTC  
> Updated_at: 2014-12-10 17:14:25 UTC  
> Url: https://github.com/boostorg/compute/pull/330#discussion_r21418702  

No longer needed?

---

## Comment 37

> Username: Mageswaran1989  
> Created_at: 2014-12-09 18:17:19 UTC  
> Updated_at: 2014-12-10 17:14:25 UTC  
> Url: https://github.com/boostorg/compute/pull/330#discussion_r21548323  

pixel.x/y/z/w is equivalent to r/g/b/a right?  
  
I had to switch the index to match with OpenCV results.

---

## Comment 38

> Username: kylelutz  
> Created_at: 2014-12-10 03:06:06 UTC  
> Updated_at: 2014-12-10 17:14:25 UTC  
> Url: https://github.com/boostorg/compute/pull/330#discussion_r21581900  

Depends on the image format. For RGBA that order is correct. But for BGRA, pixel.x/y/z/w will be b/g/r/a.

---

## Comment 39

> Username: kylelutz  
> Created_at: 2014-12-10 05:48:26 UTC  
> Updated_at: 2014-12-10 17:14:25 UTC  
> Url: https://github.com/boostorg/compute/pull/330#discussion_r21585288  

Interesting, it seems `CL_FLOAT` is not supported with `CL_BGRA` but is supported with `CL_RGBA` (see https://www.khronos.org/registry/cl/sdk/2.0/docs/man/xhtml/cl_image_format.html). I'd update this to use `CL_RGBA` (as well as the tests) and it should work. We'd just have to now make sure that when we copy a `CV_32FC4` `cv::Mat` to an `image2d` that we swap the components around (with `cv::cvtColor` or do it on the device, it would probably be good to have a generic kernel for this).

---

## Comment 40

> Username: kylelutz  
> Created_at: 2014-12-11 04:12:17 UTC  
> Url: https://github.com/boostorg/compute/pull/330#issuecomment-66566715  

Merged!

---

## Comment 41

> Username: coveralls  
> Created_at: 2014-12-11 06:24:20 UTC  
> Url: https://github.com/boostorg/compute/pull/330#issuecomment-66578310  

[![Coverage Status](https://coveralls.io/builds/1591967/badge)](https://coveralls.io/builds/1591967)  
  
Coverage increased (+0.04%) when pulling **010753db266a8a996d9ffce122206334980a7542 on Mageswaran1989:histogram_example** into **848d95601ea11fd9d29558e4f1db17cc28e84858 on kylelutz:develop**.

---
