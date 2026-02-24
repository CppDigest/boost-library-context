# #370 Update nc_chi_squared.qbk [Merged]

> Username: ambreshbiradar9  
> Created at: 2020-06-12 14:55:46 UTC  
> Updated at: 2020-06-21 20:31:50 UTC  
> Merged at: 2020-06-14 15:12:42 UTC  
> Closed at: 2020-06-14 15:12:42 UTC  
> Url: https://github.com/boostorg/math/pull/370  



---

## Review 1 [Commented]

> Username: NAThompson  
> Created_at: 2020-06-12 15:00:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/math/pull/370#pullrequestreview-429814306  

📁 doc/distributions/nc_chi_squared.qbk

```diff
  22 | 
  23 |-       // Accessor to degrees of freedom parameter v:
  23 |+       // Accessor to degrees of freedom parameter [nu]:
```

> Username: NAThompson  
> Created_at: 2020-06-12 15:00:47 UTC  
> Updated_at: 2020-06-14 03:58:33 UTC  
> Url: https://github.com/boostorg/math/pull/370#discussion_r439472982  

Does [nu] get rendered correctly in the code bracket?  
  
Run:  
  
```  
math/doc$ ../../../b2  
math$ python3 -m http.server  
```  
  
the go to `localhost:8000` in your browser.


---

## Comment 2

> Username: NAThompson  
> Created_at: 2020-06-14 13:31:09 UTC  
> Url: https://github.com/boostorg/math/pull/370#issuecomment-643767056  

@ambreshbiradar9 : Why did you mark this as a draft? It looks like everything is ready go now.

---

## Comment 3

> Username: ambreshbiradar9  
> Created_at: 2020-06-14 14:39:00 UTC  
> Url: https://github.com/boostorg/math/pull/370#issuecomment-643775636  

> @ambreshbiradar9 : Why did you mark this as a draft? It looks like everything is ready go now.  
  
ya it is ready   
sorry to convert to draft

---

## Comment 4

> Username: NAThompson  
> Created_at: 2020-06-14 15:06:51 UTC  
> Url: https://github.com/boostorg/math/pull/370#issuecomment-643779271  

Click the "Ready for review" button once it's ready to go.

---

## Comment 5

> Username: NAThompson  
> Created_at: 2020-06-14 15:13:11 UTC  
> Url: https://github.com/boostorg/math/pull/370#issuecomment-643780053  

Good job; looks like you're getting a bit more familiar with the workflow here.

---

## Comment 6

> Username: NAThompson  
> Created_at: 2020-06-14 15:21:32 UTC  
> Url: https://github.com/boostorg/math/pull/370#issuecomment-643781156  

@ambreshbiradar9 : If you're looking for a problem with a bit more meat, I broke the build for the quadrature routines because I used transitive header includes. So the correct header needs to be found and added to the quadrature routines so they compile on windows.

---

## Comment 7

> Username: ambreshbiradar9  
> Created_at: 2020-06-14 16:32:20 UTC  
> Url: https://github.com/boostorg/math/pull/370#issuecomment-643790285  

> @ambreshbiradar9 : If you're looking for a problem with a bit more meat, I broke the build for the quadrature routines because I used transitive header includes. So the correct header needs to be found and added to the quadrature routines so they compile on windows.  
  
Can you provide me the link @NAThompson   
  
Also,  
 I am having problems in building b2 engine @NAThompson I will ask you specifics if I need help

---

## Comment 8

> Username: NAThompson  
> Created_at: 2020-06-14 16:39:53 UTC  
> Url: https://github.com/boostorg/math/pull/370#issuecomment-643791226  

@ambreshbiradar9 : [Here's](https://ci.appveyor.com/project/jzmaddock/math/builds/33507129/job/kdciwnoq6huw7ssq) the build log.

---

## Comment 9

> Username: ambreshbiradar9  
> Created_at: 2020-06-20 16:38:25 UTC  
> Url: https://github.com/boostorg/math/pull/370#issuecomment-647018193  

hey @NAThompson sorry for asking this   
I am a noob   
for setting up i  have clonned boost/math but what is b2 i am having problems   
![image](https://user-images.githubusercontent.com/19732486/85206847-85123d80-b342-11ea-905d-33cda8ec9ad8.png)

---

## Comment 10

> Username: NAThompson  
> Created_at: 2020-06-20 22:16:00 UTC  
> Url: https://github.com/boostorg/math/pull/370#issuecomment-647051299  

@ambreshbiradar9 :   
  
```  
boost$ ./bootstrap.sh # should create b2  
boost$ cd libs/math/test/  
boost/libs/math/test$ ../../../b2  
```

---

## Comment 11

> Username: ambreshbiradar9  
> Created_at: 2020-06-21 19:55:47 UTC  
> Url: https://github.com/boostorg/math/pull/370#issuecomment-647174179  

Hey, @NAThompson can you link me to the problem regarding quadrature routines

---

## Comment 12

> Username: NAThompson  
> Created_at: 2020-06-21 20:31:50 UTC  
> Url: https://github.com/boostorg/math/pull/370#issuecomment-647178053  

@ambreshbiradar9 : See line 961 of [this](https://travis-ci.org/github/boostorg/math/jobs/698229545) build log.

---
