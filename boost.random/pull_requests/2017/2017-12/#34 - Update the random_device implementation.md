# #34 Update the random_device implementation [Closed]

> Username: jeking3  
> Created at: 2017-12-07 23:31:45 UTC  
> Updated at: 2017-12-08 01:43:01 UTC  
> Closed at: 2017-12-08 01:40:31 UTC  
> Url: https://github.com/boostorg/random/pull/34  

This is an adaptation of https://github.com/boostorg/uuid/pull/53, with the intention to keep random_device as the entropy provider by moving the relevant code into Boost.Random.  
  
This pull request updates the random_device implementation so that it:  
  
* Supports more platforms optimally, for example CloudABI, OpenBSD, and Windows UWP  
* Is easier to maintain, as each platform's implementation is in a separate file  
* Removes the library dependency on Boost.System  
* Is header-only, and thus makes Boost.Random header-only  
* Is well-tested for happy and sad paths  
  
Removes the token-based random_device explicit constructor.  This came up as a security issue in a code review of a similar pull request in Boost.Uuid.  Further, the token no longer makes sense with respect to the code here.  
  
Expanded the documentation in the random_device header.  
  
Adds a new exception "entropy_error" to handle errors getting entropy.  
  
Removed the detail auto_link implementation inside Boost.Random as it is no longer necessary - the one in Boost.Config is sufficient.  
  
Updated the documentation.  
  
Updated the random_device::generate method with additional checks based on code reviews for similar code in another boost project.  
  
Also added a top-level Jamfile that builds the "example" sub-directory with each build, as one of the examples needed to be updated in order to build.  This will prevent rot in the example directory.  
  
Note: Other libraries that link against Boost.Random (like Boost.Uuid) will fail to build until they stop trying to link against Boost.Random.  
  
#### CI Build Results ####  
  
https://ci.appveyor.com/project/jeking3/random/build/1.0.47-develop  
https://travis-ci.org/jeking3/random/builds/313178800  
  
This fixes #20  
This fixes #22

---

## Comment 1

> Username: swatanabe  
> Created_at: 2017-12-07 23:57:42 UTC  
> Url: https://github.com/boostorg/random/pull/34#issuecomment-350131613  

AMDG  
  
On 12/07/2017 04:31 PM, James E. King, III wrote:  
> This is an adaptation of https://github.com/boostorg/uuid/pull/53, with the intention to keep random_device as the entropy provider by moving the relevant code into Boost.Random.  
>   
> This pull request updates the random_device implementation so that it:  
>   
> * Supports more platforms optimally, for example CloudABI, OpenBSD, and Windows UWP  
> * Is easier to maintain, as each platform's implementation is in a separate file  
> * Removes the library dependency on Boost.System  
  
  So, instead of boost::system_error, you make your  
own version of the same thing.  Wonderful.  
  
> * Is header-only, and thus makes Boost.Random header-only  
  
As I've stated before, I do not consider this a benefit.  
  
> * Is well-tested for happy and sad paths  
>   
> Removes the token-based random_device explicit constructor.  This came up as a security issue in a code review of a similar pull request in Boost.Uuid.  Further, the token no longer makes sense with respect to the code here.  
>   
> Expanded the documentation in the random_device header.  
>   
> Adds a new exception "entropy_error" to handle errors getting entropy.  
>   
> Removed the detail auto_link implementation inside Boost.Random as it is no longer necessary - the one in Boost.Config is sufficient.  
>   
> Updated the documentation.  
>   
  
  Please do not modify the initial example on  
the front page.  It's intended to be as simple  
and dumb and minimal as possible.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: jeking3  
> Created_at: 2017-12-08 00:11:33 UTC  
> Url: https://github.com/boostorg/random/pull/34#issuecomment-350133846  

Thanks @swatanabe - is this something that has a chance of being merged, or should I drop it?    
  
I modified the example on the documentation page because it is misleading.  The example as stated will always produce the same random number sequence from the default-seeded mersenne twister, and I find that to be a misleading or poor example for others to follow.  Do you disagree?  
  
I'm trying to put the code for entropy generation into the right place... It's not my intention to upset you here.  I just want Boost.Uuid out of the business of entropy generation, and can provide the additional benefits listed.

---

## Comment 3

> Username: swatanabe  
> Created_at: 2017-12-08 00:57:31 UTC  
> Url: https://github.com/boostorg/random/pull/34#issuecomment-350140862  

AMDG  
  
On 12/07/2017 05:11 PM, James E. King, III wrote:  
> Thanks @swatanabe - is this something that has a chance of being merged, or should I drop it?    
>   
  
As given, I will not merge it.  Regarding specific issues:  
+ Platform support  
0 Splitting files  
- Replacing Boost.System  
- Header only  
+ Better testing  
  
> I modified the example on the documentation page because it is misleading.  The example as stated will always produce the same random number sequence from the default-seeded mersenne twister, and I find that to be a misleading or poor example for others to follow.  Do you disagree?  
>   
  
  Yes, I disagree.  This is normal behavior for PRNGs.  
Seeding does need to be discussed, but this example  
is not the right place.  
  
> I'm trying to put the code for entropy generation into the right place... It's not my intention to upset you here.  I just want Boost.Uuid out of the business of entropy generation, and can provide the additional benefits listed.  
>   
  
In Christ,  
Steven Watanabe

---

## Comment 4

> Username: jeking3  
> Created_at: 2017-12-08 01:10:32 UTC  
> Url: https://github.com/boostorg/random/pull/34#issuecomment-350142851  

Okay, so, if I restore the example, restore the library, eliminate the entropy_error, fix the documentation to reflect that, and put the random provider implementations into a library then there is a possibility this can be considered for merge - correct?  Will you accept if the cpp file just includes those two headers, and I move the .ipp files into src with the cpp that includes them, or do you want me to make one amalgamation of all the files with preprocessor barriers?  Let me know, we can get this done.  I'm motivated to make it happen.

---

## Comment 5

> Username: jeking3  
> Created_at: 2017-12-08 01:40:31 UTC  
> Url: https://github.com/boostorg/random/pull/34#issuecomment-350147332  

Wait, nevermind, my work in Boost.Uuid cannot be rerouted here if this implementation relies on a library.  I will drop this work.

---

## Comment 6

> Username: swatanabe  
> Created_at: 2017-12-08 01:43:01 UTC  
> Url: https://github.com/boostorg/random/pull/34#issuecomment-350147724  

AMDG  
  
  That sounds good.  I will, of course, need to  
review the changes in detail, which could take  
some time.  I don't care that much about how the  
source is organized as long as it's all under src/  
and the sources are safe to glob.  (To keep things  
simple for anyone who wants to use his own  
favorite build tool.)  
  
In Christ,  
Steven Watanabe

---
