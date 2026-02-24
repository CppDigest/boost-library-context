# #3 [CMake] Add minimal cmake file [Merged]

> Username: Mike-Devel  
> Created at: 2018-09-25 16:40:43 UTC  
> Updated at: 2018-11-12 16:18:37 UTC  
> Merged at: 2018-11-12 16:18:37 UTC  
> Closed at: 2018-11-12 16:18:37 UTC  
> Url: https://github.com/boostorg/io/pull/3  

Generate cmake target that can be used by other libraries  
to express their dependency on this library and retrieve  
any configuration information such as the include directory,  
transitive dependencies, necessary compiler options or the  
required c++ standards level.  
  
For more information: https://groups.google.com/forum/#!topic/boost-developers-archive/kM4JRmyVl3M

---

## Comment 1

> Username: Mike-Devel  
> Created_at: 2018-10-31 10:25:59 UTC  
> Updated_at: 2018-10-31 10:26:39 UTC  
> Url: https://github.com/boostorg/io/pull/3#issuecomment-434635760  

Any comment on this PR? Don't you want to supporr cmake at all? Don't you like this particular PR? Or do you prefer to wait for a full fledged, boost wide solution?

---

## Comment 2

> Username: eldiener  
> Created_at: 2018-11-02 23:29:43 UTC  
> Url: https://github.com/boostorg/io/pull/3#issuecomment-435538143  

As with all repositories you need a test.

---

## Comment 3

> Username: Mike-Devel  
> Created_at: 2018-11-12 13:02:29 UTC  
> Url: https://github.com/boostorg/io/pull/3#issuecomment-437871879  

> As with all repositories you need a test.  
  
Well, so far, not all maintainers wanted to have a test for the cmake file, had different opinions on how the test should look like or didn't want anything to do with this at all. As I don't know who is in charge of which repository (should have checked beforehand -sorry) I decided to initially only submit the basic cmake file and then wait for feedback.   
  
I hope this doesn't get annoying for you - next time I'll make sure to check beforehand.  
  
Anyway, I added the test to travis and once it completes successfully (apparently there are many other boost tests running right now) you can merge the PR if you feel comfortable with it.

---
