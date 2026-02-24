# #47 add ci bits and readme [Merged]

> Username: jeking3  
> Created at: 2018-03-07 18:44:43 UTC  
> Updated at: 2018-03-15 19:28:46 UTC  
> Merged at: 2018-03-11 22:26:57 UTC  
> Closed at: 2018-03-11 22:26:57 UTC  
> Url: https://github.com/boostorg/program_options/pull/47  

### Summary ###  
  
This pull request adds support for:  
  
* Travis CI  
  * Various gcc and clang builds  
  * cppcheck  
  * ubsan  
  * covecov.io build  
* Appveyor  
  * Visual Studio 2017 and 2013 builds  
  * mingw 32-bit and 64-bit builds  
  * cygwin 32-bit and 64-bit builds  
* codecov.io integration, which will post code coverage deltas into each pull request that runs successfully (really cool!)  
* Coverity Scan support (when enabled), also see #46 which came out of this work.  
  
### Example Build Jobs ###  
  
Appveyor: https://ci.appveyor.com/project/jeking3/program-options/build/1.0.16-develop  
(note the red builds are already tracked in #45 so I made these build failures non-fatal)  
  
Travis: https://travis-ci.org/jeking3/program_options/builds/350461368  
(note the fialed UBSAN issue is tracked in #44 but this is serious so I left it as fatal!)  
  
### Work Required by the Repository Owner ###  
  
After merging this pull request the repository owner must perform a number of tasks:  
  
* Add the boostorg/program_options project to your Travis CI account.  Make sure it is the boostorg one.  Sometimes you have to click on "sync with github" to get it to show up.  If it does not show up you will need to request Admin level access through the Boost Admin list.  
* Add the boostorg/program_options project to your Appveyor account.  
* Update the README.md at the top, uncommenting the status section and fixing all the links to use your project.  
* I will create a Coverity Scan project after this pull request is merged and add the repository owner as an owner/admin; the repository owner will then need to modify the boostorg/program_options travis project with two environment variables that enable coverity scan builds to work.  
  
This fixes #43

---

## Comment 1

> Username: vprus  
> Created_at: 2018-03-11 22:44:25 UTC  
> Url: https://github.com/boostorg/program_options/pull/47#issuecomment-372156528  

I've merged this pull request, thanks. One thing that's not clear is how do I make the appveyor links for specific branches?

---

## Comment 2

> Username: jeking3  
> Created_at: 2018-03-12 11:56:10 UTC  
> Url: https://github.com/boostorg/program_options/pull/47#issuecomment-372285277  

The way appveyor is set up, when you add a boostorg repository to your account as admin, it makes a random length:5 identifier for your account access to it.  I'm not entirely sure how it works but you can look at how it is done in DateTime, Format, or Uuid.  The only problem I know is that regular folks cannot click on the link and see the build results; only repository admins can.  
  
For example, this is the "master" build for DateTime: https://ci.appveyor.com/project/jeking3/date-time-1evbf/branch/master and the "develop" one: https://ci.appveyor.com/project/jeking3/date-time-1evbf/branch/develop  
  
You'll get the right shield/badge showing the status of the build of each but clicking on them for other people gives an access denied, which is okay.  This is really to run CI on pull requests so people can self-service their PRs and get the ready for code review.

---

## Comment 3

> Username: vprus  
> Created_at: 2018-03-15 19:16:04 UTC  
> Url: https://github.com/boostorg/program_options/pull/47#issuecomment-373492039  

For me, appveyor has generated URLs for badges that have different form, but seem to work. Next question - the job at https://travis-ci.org/boostorg/program_options/jobs/352106619 appears to fail because it cannot download coverity, is this expected?

---

## Comment 4

> Username: jeking3  
> Created_at: 2018-03-15 19:28:46 UTC  
> Url: https://github.com/boostorg/program_options/pull/47#issuecomment-373495565  

Coverity Scan has been offline for a number of weeks.  Not sure why.   I believe the build job in travis is disabled; you can hide that in the README for now.

---
