# #251 - Add continuous integration builds to improve code and pull request quality [Closed]

> Username: jeking3  
> Created at: 2017-10-20 20:36:13 UTC  
> Updated at: 2019-08-26 02:36:40 UTC  
> Closed at: 2019-08-26 02:36:40 UTC  
> Url: https://github.com/boostorg/spirit/issues/251  

Using these free CI services improves quality of code submissions and makes maintenance of a library a little easier, as the maintainer is freed in many cases from needing to qualify new code manually.  
  
If you are familiar with these services, you can take the relevant files from Boost.Format or Boost.Uuid as a reference, or if you'd like I can submit a pull request containing the files you need with the combinations of windows, linux, and osx compilers you would like to qualify in pull request builds.  Then all you would need to do is enable the boostorg builds in appveyor and travis.  
  
I can also help get the project set up in Coverity Scan, if you are interested.  I have Boost.Uuid and Boost.Format set to build the master branch on every push into it plus weekly, with a job that only runs against master which updates Coverity, all automated.

---

## Comment 1

> Username: djowel  
> Created at: 2017-10-27 00:27:43 UTC  
> Url: https://github.com/boostorg/spirit/issues/251#issuecomment-339838366  

Absolutely! A PR would be great!

---

## Comment 2

> Username: jeking3  
> Created at: 2017-10-27 00:28:54 UTC  
> Url: https://github.com/boostorg/spirit/issues/251#issuecomment-339838532  

If you copy the .travis.yml file from the uuid project it will get you started.  You need admin access to enable CI builds.... so I can't help get them running but I can send you a PR for those if you want.

---

## Comment 3

> Username: Kojoley  
> Created at: 2017-10-27 12:04:04 UTC  
> Url: https://github.com/boostorg/spirit/issues/251#issuecomment-339952768  

I setup travis for spirit about a year ago https://travis-ci.org/Kojoley/spirit but have not opened a PR because spirit does not pass all it's tests (there were a lot of problems like fusion varadics, fails with new optional, broken examples and repository on c++11).

---

## Comment 4

> Username: jeking3  
> Created at: 2017-10-27 20:49:34 UTC  
> Url: https://github.com/boostorg/spirit/issues/251#issuecomment-340081707  

I'll have to agree, sadly, I was unable to get a build to complete for the following reasons:  
  
1. On appveyor the build took over an hour and was killed.  That's just one build job too.  
2. On travis the log file limit of 4MB was exceeded because of the number of verbose compiler warnings.  
  
I'm afraid I can't help much with these issues.  Perhaps the build needs to be split up into smaller digestable pieces.  I see a number of libraries here, so running multiple build jobs, one per library, might work I suppose.  
  
How do you qualify changes to the library with a turnaround time that is this long for a build?  It must be quite painful...

---

## Comment 5

> Username: djowel  
> Created at: 2017-10-27 22:00:45 UTC  
> Url: https://github.com/boostorg/spirit/issues/251#issuecomment-340106640  

I agree the tests should be broken down into smaller components; i.e lexer, qi, karma and x3. That should be doable. Also, fusion is now hooked up, and those errors you mention were already fixed.
