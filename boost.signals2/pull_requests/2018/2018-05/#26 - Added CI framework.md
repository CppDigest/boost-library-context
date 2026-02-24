# #26 Added CI framework [Merged]

> Username: jeking3  
> Created at: 2018-05-27 15:43:21 UTC  
> Updated at: 2018-05-28 00:20:34 UTC  
> Merged at: 2018-05-27 23:05:20 UTC  
> Closed at: 2018-05-27 23:05:20 UTC  
> Url: https://github.com/boostorg/signals2/pull/26  

- travis with valgrind, cppcheck, ubsan, codecov, covscan (future)  
  - appveyor with MSVC 2010 through 2017, cygwin 32/64, mingw 32/64  
  - README, LICENSE, etc.  
  
Action items following merge of this PR, assuming it happens:  
  
1. I will open a Coverity Scan project and invite the maintainer.  
2. Enable boostorg/signals2 in Travis CI by adding a new project.  
3. Enable boostorg/signals2 in Appveyor by adding a new project.  
4. Add COVERITY_SCAN_NOTIFICATION_EMAIL to the travis build settings.  
5. Add COVERITY_SCAN_TOKEN as a hidden variable to the travis build settings.  
6. Update the README.md file fixing the links for the badges and projects.  
  
I'll provide assistance in getting these set up.  In fact, I may create a small personal github repository to instruct maintainers on how to add this set of CI to their boostorg repositories.  It only requires a few hours of total time, typically.  
  
When complete, all pull requests will build in travis and appveyor, have codecov.io code coverage reports, and coverity scan static code analysis, as well as cppcheck, valgrind, and UBSAN enabled, and there are builds on Windows, Linux, and Mac with many different compilers.  
  
### Builds from my Fork ###  
  
Here you can see the result (without Coverity Scan enabled):  
  
Appveyor: https://ci.appveyor.com/project/jeking3/signals2  
Travis CI: https://travis-ci.org/jeking3/signals2/builds/384356558

---
