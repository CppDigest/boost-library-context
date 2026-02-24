# #6 - Add continuous integration builds on Appveyor to improve code and pull request quality [Closed]

> Username: jeking3  
> Created at: 2017-10-23 15:09:41 UTC  
> Updated at: 2019-01-16 18:35:29 UTC  
> Closed at: 2019-01-16 18:35:29 UTC  
> Url: https://github.com/boostorg/timer/issues/6  

Using these free CI services improves quality of code submissions and makes maintenance of a library a little easier, as the maintainer is freed in many cases from needing to qualify new code manually.  
  
If you are familiar with these services, you can take the relevant files from Boost.Format or Boost.Uuid as a reference, or if you'd like I can submit a pull request containing the files you need with the combinations of windows, linux, and osx compilers you would like to qualify in pull request builds. Then all you would need to do is enable the boostorg builds in appveyor and travis.  
  
Given this module likely depends on Windows APIs that are not available in all families, you may want to use the Boost.WinAPI versions of these files instead as they will provide more coverage.  
  
I can also help get the project set up in Coverity Scan, if you are interested. I have Boost.Uuid and Boost.Format set to build the master branch on every push into it plus weekly, with a job that only runs against master which updates Coverity, all automated.
