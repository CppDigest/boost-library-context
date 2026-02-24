# #2 Drone config [Merged]

> Username: sdarwin  
> Created at: 2021-01-12 17:11:36 UTC  
> Updated at: 2021-01-21 22:02:27 UTC  
> Merged at: 2021-01-21 22:02:27 UTC  
> Closed at: 2021-01-21 22:02:27 UTC  
> Url: https://github.com/boostorg/tti/pull/2  

Drone is a continuous integration framework similar to Travis CI. [The C++ Alliance](https://cppalliance.org/) is offering a hosted Drone server for Boost libraries. Please refer to https://github.com/CPPAlliance/drone-ci for more information and instructions.

---

## Comment 1

> Username: eldiener  
> Created_at: 2021-01-13 02:05:40 UTC  
> Url: https://github.com/boostorg/tti/pull/2#issuecomment-759154797  

Why is 5 separate files necessary as opposed to 2. You are certainly allowed to invoke a shell script with different arguments in order to have different paths of execution within that script. Please reduce drone files to a single .star file and a single shell script. I will consider using drone for Boost libraries I maintain if you do this, but having more than 2 files seems to me an utter waste and a poor design for a CI testing service.

---

## Comment 2

> Username: sdarwin  
> Created_at: 2021-01-13 02:40:30 UTC  
> Url: https://github.com/boostorg/tti/pull/2#issuecomment-759165872  

> Why is 5 separate files necessary as opposed to 2.  
  
These files were generated in a batch with a script designed to handle any possible travis.yml file.  Many of those have complex "before" and "after" sections, where are shared between jobs.    If we look at the resulting files from only one particular repository, such as tti, the results don't appear "optimized".    But, they are functional.  :-)       
  
I will make a note to update this pull request for tti, by customizing it manually, and removing the extra files.

---

## Comment 3

> Username: eldiener  
> Created_at: 2021-01-13 02:55:47 UTC  
> Url: https://github.com/boostorg/tti/pull/2#issuecomment-759170631  

Why not just update your script so that it just generates the two files I suggested with different arguments to the single .sh file invoking the same functionality that you now have in four separate .sh files. Really how hard can that be compared to what you are currently doing in creating these drone files. Furthermore you can standardize the two files you distribute to any single Boost library as drone.star and drone.sh. My objection is not what you have done in offering a better CI service. My objection is how you have made it complicated enough that you are distributing it as five ( or more ) files whereas normal CI testing files are usually the single .yml file. I understand your argument in needing a shell script aside from a yml like drone file. I just do not understand why you would want ot need to generate multiple shell scripts when Linux shell programming easily allows different paths of functionality in a single shell script. Nor am I arguing that Linux shellscript programming should jam all functionality into a single shell script, but I am arguing that you should do this because you are distributing this to Boost libraries as a CI testing solution and CI testing files should remain as minimal as possible in their amount of files.

---

## Comment 4

> Username: sdarwin  
> Created_at: 2021-01-13 19:48:32 UTC  
> Updated_at: 2021-01-13 19:48:52 UTC  
> Url: https://github.com/boostorg/tti/pull/2#issuecomment-759699869  

The # of files have been reduced to 2.

---
