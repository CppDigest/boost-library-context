# #466 - Multiple test failures in cubic_hermite_test with Apple Clang 12.0 and C++17 [Closed]

> Username: evanmiller  
> Created at: 2020-12-23 01:09:33 UTC  
> Updated at: 2021-01-08 15:45:08 UTC  
> Closed at: 2021-01-08 15:45:08 UTC  
> Url: https://github.com/boostorg/math/issues/466  

Full log:  
  
https://github.com/evanmiller/math/runs/1597837166  
  
Relevant portion: (starts at line 565 of "Test" above)  
  
```  
testing.capture-output ../../../bin.v2/libs/math/test/cubic_hermite_test.test/clang-darwin-12.0/debug/cxxstd-17-iso/link-static/threading-multi/visibility-hidden/cubic_hermite_test.run  
====== BEGIN OUTPUT ======  
Error at cubic_hermite_test.cpp:test_quadratic:178:  
  ULP distance in float precision is 84, which exceeds 65, error/ulps  = 1.29.  
  Expected: +28.713123322 = +0x1.cb68f4p+4  
  Computed: +28.712963104 = +0x1.cb684cp+4  
  Mollified relative error: +5.57993235e-06  
Error at cubic_hermite_test.cpp:test_quadratic:178:  
  ULP distance in float precision is 107, which exceeds 65, error/ulps  = 1.65.  
  Expected: +28.725624084 = +0x1.cb9c28p+4  
  Computed: +28.725419998 = +0x1.cb9b52p+4  
  Mollified relative error: +7.10467793e-06  
Error at cubic_hermite_test.cpp:test_quadratic:178:  
  ULP distance in float precision is 124, which exceeds 65, error/ulps  = 1.91.  
  Expected: +28.738124847 = +0x1.cbcf5cp+4  
  Computed: +28.737888336 = +0x1.cbce64p+4  
  Mollified relative error: +8.22987658e-06  
Error at cubic_hermite_test.cpp:test_quadratic:178:  
  ULP distance in float precision is 133, which exceeds 65, error/ulps  = 2.05.  
  Expected: +28.750625610 = +0x1.cc029p+4  
  Computed: +28.750371933 = +0x1.cc0186p+4  
  Mollified relative error: +8.82336826e-06  
Error at cubic_hermite_test.cpp:test_quadratic:178:  
  ULP distance in float precision is 137, which exceeds 65, error/ulps  = 2.11.  
  Expected: +28.763126373 = +0x1.cc35c4p+4  
  Computed: +28.762865067 = +0x1.cc34b2p+4  
  Mollified relative error: +9.08478341e-06  
Error at cubic_hermite_test.cpp:test_quadratic:178:  
  ULP distance in float precision is 135, which exceeds 65, error/ulps  = 2.08.  
  Expected: +28.775627136 = +0x1.cc68f8p+4  
  Computed: +28.775369644 = +0x1.cc67eap+4  
  Mollified relative error: +8.94826917e-06  
Error at cubic_hermite_test.cpp:test_quadratic:178:  
  ULP distance in float precision is 125, which exceeds 65, error/ulps  = 1.92.  
  Expected: +28.788127899 = +0x1.cc9c2cp+4  
  Computed: +28.787889481 = +0x1.cc9b32p+4  
  Mollified relative error: +8.28183693e-06  
Error at cubic_hermite_test.cpp:test_quadratic:178:  
  ULP distance in float precision is 110, which exceeds 65, error/ulps  = 1.69.  
  Expected: +28.800628662 = +0x1.cccf6p+4  
  Computed: +28.800418854 = +0x1.ccce84p+4  
  Mollified relative error: +7.28485293e-06  
Error at cubic_hermite_test.cpp:test_quadratic:178:  
  ULP distance in float precision is 89, which exceeds 65, error/ulps  = 1.37.  
  Expected: +28.813129425 = +0x1.cd0294p+4  
  Computed: +28.812959671 = +0x1.cd01e2p+4  
  Mollified relative error: +5.89155115e-06  
Error at cubic_hermite_test.cpp:test_quadratic:178:  
  ULP distance in long double precision is 80, which exceeds 65, error/ulps  = 1.23.  
  Expected: +19.491217344654242575250 = +0x9.bee035bf424b9afp+1  
  Computed: +19.491217344654242714028 = +0x9.bee035bf424b9ffp+1  
  Mollified relative error: +7.12002106508788549447e-18  
Error at cubic_hermite_test.cpp:test_quadratic:178:  
  ULP distance in long double precision is 91, which exceeds 65, error/ulps  = 1.4.  
  Expected: +19.503717344654242575944 = +0x9.c079cf58dbe5349p+1  
  Computed: +19.503717344654242733804 = +0x9.c079cf58dbe53a4p+1  
  Mollified relative error: +8.0938332690284354886e-18  
Error at cubic_hermite_test.cpp:test_quadratic:178:  
  ULP distance in long double precision is 92, which exceeds 65, error/ulps  = 1.42.  
  Expected: +19.516217344654242576638 = +0x9.c21368f2757ece3p+1  
  Computed: +19.516217344654242736232 = +0x9.c21368f2757ed3fp+1  
  Mollified relative error: +8.17753548095124968893e-18  
Error at cubic_hermite_test.cpp:test_quadratic:178:  
  ULP distance in long double precision is 82, which exceeds 65, error/ulps  = 1.26.  
  Expected: +19.528717344654242577331 = +0x9.c3ad028c0f1867dp+1  
  Computed: +19.528717344654242719579 = +0x9.c3ad028c0f186cfp+1  
  Mollified relative error: +7.28400757303380804403e-18  
Error count: 13, total ulp distance = 1389  
  
EXIT STATUS: 1  
====== END OUTPUT ======  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-12-23 12:02:10 UTC  
> Url: https://github.com/boostorg/math/issues/466#issuecomment-750226442  

@NAThompson

---

## Comment 2

> Username: NAThompson  
> Created at: 2020-12-23 23:41:42 UTC  
> Url: https://github.com/boostorg/math/issues/466#issuecomment-750578070  

Did Apple Clang change the rounding mode? This barrage of failures is bizarre. . .

---

## Comment 3

> Username: NAThompson  
> Created at: 2021-01-06 21:40:06 UTC  
> Updated at: 2021-01-06 21:40:30 UTC  
> Url: https://github.com/boostorg/math/issues/466#issuecomment-755730951  

Should be fixed in #482.

---

## Comment 4

> Username: evanmiller  
> Created at: 2021-01-08 15:45:08 UTC  
> Url: https://github.com/boostorg/math/issues/466#issuecomment-756825648  

Confirm fixed.
