# #210 Add additional debugging flags to drone GCC ASAN [Closed]

> Username: mborland  
> Created at: 2024-06-25 13:41:27 UTC  
> Updated at: 2024-07-01 12:19:33 UTC  
> Closed at: 2024-06-25 18:09:57 UTC  
> Url: https://github.com/boostorg/charconv/pull/210  

Drone log from PR #209 shows asan failure without any debug information or stacktrace.

---

## Comment 1

> Username: pdimov  
> Created_at: 2024-06-25 14:11:42 UTC  
> Url: https://github.com/boostorg/charconv/pull/210#issuecomment-2189074632  

Both of these options should already be present. `-g` is supplied by `<debug-symbols>on`:  
  
https://github.com/bfgroup/b2/blob/3c0f5630157c3db1025b54c2f6da25cf234b8e20/src/tools/gcc.jam#L678  
  
and `-fno-omit-frame-pointer` is supplied by `<address-sanitizer>norecover`:  
  
https://github.com/bfgroup/b2/blob/3c0f5630157c3db1025b54c2f6da25cf234b8e20/src/tools/gcc.jam#L695  
  
The features, in turn, are supplied by drone.sh when ASAN is set:  
  
https://github.com/boostorg/charconv/blob/9db7af735e435132d4849ad02a08eb7579103601/.drone/drone.sh#L28

---

## Comment 2

> Username: mborland  
> Created_at: 2024-06-25 14:24:55 UTC  
> Url: https://github.com/boostorg/charconv/pull/210#issuecomment-2189112648  

Thanks as always @pdimov. I was hoping to get more since these failures seem extremely odd like limits_link_test having a seg fault on Drone while the fuzzers on GHA are fine. I'll try adding the same platform ASAN to GHA.

---

## Comment 3

> Username: pdimov  
> Created_at: 2024-06-25 14:41:33 UTC  
> Url: https://github.com/boostorg/charconv/pull/210#issuecomment-2189151914  

The failures in #209 are a bit weird because (a) everything fails and (b) the push Drone job (which should be testing the same thing) succceeds.

---

## Comment 4

> Username: mborland  
> Created_at: 2024-06-25 14:58:21 UTC  
> Url: https://github.com/boostorg/charconv/pull/210#issuecomment-2189191412  

> The failures in #209 are a bit weird because (a) everything fails and (b) the push Drone job (which should be testing the same thing) succceeds.  
  
Yeah the ASAN GCC-12 runs fine on GHA. @sdarwin could this possibly be related to the image changes you made on Drone yesterday? The [push job](https://drone.cpp.al/boostorg/charconv/929) ran 4 days ago.

---

## Comment 5

> Username: mborland  
> Created_at: 2024-06-25 15:31:04 UTC  
> Url: https://github.com/boostorg/charconv/pull/210#issuecomment-2189269946  

I'm seeing the odd segmentation faults in decimal as well: https://drone.cpp.al/cppalliance/decimal/1092/40/2. Many of these test are purely math operations.

---

## Comment 6

> Username: sdarwin  
> Created_at: 2024-06-25 17:55:19 UTC  
> Url: https://github.com/boostorg/charconv/pull/210#issuecomment-2189620906  

PR 211 succeeded in Drone

---
