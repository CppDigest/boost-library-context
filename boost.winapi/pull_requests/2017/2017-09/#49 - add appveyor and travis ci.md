# #49 add appveyor and travis ci [Merged]

> Username: jeking3  
> Created at: 2017-09-15 14:30:20 UTC  
> Updated at: 2017-09-15 18:06:58 UTC  
> Merged at: 2017-09-15 18:06:44 UTC  
> Closed at: 2017-09-15 18:06:44 UTC  
> Url: https://github.com/boostorg/winapi/pull/49  

This pull request adds extensive CI testing on Appveyor and a minimal travis build to make sure linux didn't break.  On Appveyor many combinations of MSVC, Cygwin, MinGW, and MinGW-w64 environments are checked as well as all of the ``_WIN32_WINNT`` compile-time branches, and ``BOOST_USE_WINDOWS_H``.  The intention is to catch problems before they enter the codebase.  The CI build itself takes about 45 minutes when running one build job at a time.  
  
To see the build results with these changes: jeking3#1  
  
Note the PR build picked up four errors in develop that need to be addressed to get to a clean build.  This proves that the winapi headers in develop are not fully compatible with all target Windows platforms and validates the effort to add CI to the project.  These will be addressed with separate pull requests.  
  
This fixes #46.

---

## Comment 1

> Username: Lastique  
> Created_at: 2017-09-15 15:32:58 UTC  
> Url: https://github.com/boostorg/winapi/pull/49#issuecomment-329817323  

Thank you for the effort of adding CI.  
  
Locally I test on MSVC versions since 7.1, is it possible to add those to appveyor.yml?  
  
> Note the PR build picked up four errors in develop that need to be addressed to get to a clean build. This proves that the winapi headers in develop are not fully compatible with all target Windows platforms and validates the effort to add CI to the project.  
  
This is unsurprising since non-desktop targets were never really supported.

---

## Comment 2

> Username: jeking3  
> Created_at: 2017-09-15 15:37:02 UTC  
> Updated_at: 2017-09-15 15:41:27 UTC  
> Url: https://github.com/boostorg/winapi/pull/49#issuecomment-329818444  

@Lastique The UWP windows store compatibility is a bigger task, but the CI job picked up three other issues in older platform releases that need addressing so I opened up issues for each.  
  
Appveyor goes back to Visual Studio 2008.  I added msvc-9.0 and re-enabled Visual Studio 2012, and 2013, and I marked the windows store UWP job as an expected failure.

---

## Comment 3

> Username: Lastique  
> Created_at: 2017-09-15 15:41:24 UTC  
> Url: https://github.com/boostorg/winapi/pull/49#issuecomment-329819666  

> Appveyor goes back to Visual Studio 2008.  
  
That's too bad because 2003 and 2005 were the ones shipping their own Windows SDK, which is significantly different from the separate Windows SDK 6.0 that was used with 2008 and later. This means I will have to keep testing manually as well.  
  
> Do you want the 2012, 2013 jobs re-enabled as well?  
  
Sure, the more testing the better.

---

## Comment 4

> Username: jeking3  
> Created_at: 2017-09-15 15:45:51 UTC  
> Url: https://github.com/boostorg/winapi/pull/49#issuecomment-329820917  

If there are installers for 2003 and 2005 that are publicly downloadable they could be added to the build job.  
  
I would highly recommend deprecating support for any compiler before Visual Studio 2010 at this point.  It will simplify text matrices and folks already have access to older boost releases that support the older compiler environments.  I'd have to guess the percentage of folks using an environment that's over 7 years old to be under 2% of the population (that's just a guess, and might be quite wrong).  At some point Appveyor will drop msvc-9.0 from their build image as well - likely next year when it reaches a decade old.

---

## Comment 5

> Username: jeking3  
> Created_at: 2017-09-15 15:51:50 UTC  
> Url: https://github.com/boostorg/winapi/pull/49#issuecomment-329822626  

There's no proof the older compilers are using the older Platform SDKs either, given they are all smashed onto a single build image.  I didn't look into explicitly selecting a Platform SDK (like 6.0A, 7.0, 7.1, 8.0, 10.0 for which there are 5 builds) as part of this.  It would be useful, and perhaps a worthy enhancement in the future.  I wonder if there is a universal way across all SDKs and build environments to print out the platform sdk version in use (including cygwin, mingw, mingw-w64, and msvc-9.0 though msvc-14.1)?

---

## Comment 6

> Username: Lastique  
> Created_at: 2017-09-15 16:05:25 UTC  
> Url: https://github.com/boostorg/winapi/pull/49#issuecomment-329826330  

> If there are installers for 2003 and 2005 that are publicly downloadable they could be added to the build job.  
  
There might be, but wouldn't that mean that every time the tests are run the installer would need to be downloaded and run? BTW, the installers are likely interactive-only (i.e. not scriptable).  
  
> I would highly recommend deprecating support for any compiler before Visual Studio 2010 at this point.  
  
That would be unfortunate given that the main purpose of Boost.WinAPI is to be a compatibility layer for various versions of Windows SDK. There probably aren't many users of MSVC 7.1 there, but 8 and 9 might still have users.  
  
> There's no proof the older compilers are using the older Platform SDKs either, given they are all smashed onto a single build image.  
  
This is important. In fact, the Windows SDK version is much more important than MSVC version for Boost.WinAPI. Normally, Windows SDK is selected by the command line scripts corresponding to the particular MSVC version. It is possible to have multiple MSVC versions on the same machine and have each of them use their respective Windows SDK - that's what I have locally.  
  
> I wonder if there is a universal way across all SDKs and build environments to print out the platform sdk version in use (including cygwin, mingw, mingw-w64, and msvc-9.0 though msvc-14.1)?  
  
I think, the most reliable way is to print environment variables like `INCLUDE` and whatever the equivalent for libraries is before running the tests. There's also `winapi_info` test that is run during testing; it outputs various macro values and could also print something indicating Windows SDK version as well. The only problem is that its output is saved to a file by `b2` and not printed to the console, I'm not sure how to change it to output to the console as well.

---

## Comment 7

> Username: jeking3  
> Created_at: 2017-09-15 16:12:30 UTC  
> Url: https://github.com/boostorg/winapi/pull/49#issuecomment-329828232  

> There might be, but wouldn't that mean that every time the tests are run the installer would need to be downloaded and run? BTW, the installers are likely interactive-only (i.e. not scriptable).  
  
Correct it would need to be downloaded and then installed for that build job.  Most of the Microsoft installers can be made to run headless from what I've seen.  
  
> Normally, Windows SDK is selected by the command line scripts corresponding to the particular MSVC version.  
  
Could you share how you select the Platform SDK / Windows Kit on the command line?  I can work that into the CI build job.  That way we can exercise all of the SDK revisions properly in the CI job.  
  
> I think, the most reliable way is to print environment variables like INCLUDE and whatever the equivalent for libraries is before running the tests.   
  
I can add some additional build job output to appveyor, for example just dumping the environment with "SET" should be enough to get lots of information.  I'll hold off doing this until I have a way to select the SDK with the build job however.

---

## Comment 8

> Username: Lastique  
> Created_at: 2017-09-15 16:48:36 UTC  
> Url: https://github.com/boostorg/winapi/pull/49#issuecomment-329837007  

> Correct it would need to be downloaded and then installed for that build job. Most of the Microsoft installers can be made to run headless from what I've seen.  
  
That would add quite some time to the testing duration. Hmm...  
  
Anyway, I couldn't find a 2003 installer right away, but 2005 seems to be available:  
  
http://download.microsoft.com/download/8/3/a/83aad8f9-38ba-4503-b3cd-ba28c360c27b/ENU/vcsetup.exe  
  
This also other links are from here: https://stackoverflow.com/questions/1330852/where-to-download-visual-studio-express-2005  
  
After the installation it is worthwhile to also install SP1: https://download.microsoft.com/download/6/3/c/63c69e5d-74c9-48ea-b905-30ac3831f288/VS80sp1-KB926601-X86-ENU.exe  
  
> Could you share how you select the Platform SDK / Windows Kit on the command line?  
  
When I run tests I simply specify `toolset=msvc-X`, where X is the version I need. `b2` automatically finds the environment setup scripts in the default MSVC installation directories and sets up the environment for the given MSVC version, including the directories for the corresponding Windows SDK.  
  
When I compile something manually I have to open the console corresponding to the MSVC version I need (there is typically a shortcut in the MSVC folder in the Start menu). The path and name of the script that sets up the environment is different for different MSVC versions, but most have `C:\Program Files (x86)\Microsoft Visual Studio X.Y\VC\vcvarsall.bat`, which can be invoked with an argument indicating the host/target architecture. You will probably not need to call it in the testing script, though.

---

## Review 9 [Commented]

> Username: Lastique  
> Created_at: 2017-09-15 17:00:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/winapi/pull/49#pullrequestreview-63103085  

📁 appveyor.yml

```diff
  57 |+     - COMMENT: Use Windows.H
  58 |+       TOOLSET: msvc-14.1
  59 |+       DEFINES: define="BOOST_USE_WINDOWS_H"
```

> Username: Lastique  
> Created_at: 2017-09-15 17:00:44 UTC  
> Updated_at: 2017-09-15 17:44:09 UTC  
> Url: https://github.com/boostorg/winapi/pull/49#discussion_r139200134  

You should not define this. The library tests are compiled multiple times in different configurations, one of them being with `BOOST_USE_WINDOWS_H` defined. By defining it in the command line you effectively disable tests that are supposed to be compiled without it.

> Username: jeking3  
> Created_at: 2017-09-15 17:08:16 UTC  
> Updated_at: 2017-09-15 17:44:09 UTC  
> Url: https://github.com/boostorg/winapi/pull/49#discussion_r139201585  

Okay great, that removes a build job - I'm all for that.


---

## Comment 10

> Username: jeking3  
> Created_at: 2017-09-15 17:16:49 UTC  
> Url: https://github.com/boostorg/winapi/pull/49#issuecomment-329843842  

> ``b2`` automatically finds the environment setup scripts in the default MSVC installation directories and sets up the environment for the given MSVC version, including the directories for the corresponding Windows SDK.  
  
I have Visual Studio 2010 through 2017 locally, and various platform SDKs installed.  I'm running a b2 build on toolset=msvc-10.0 with lots of debugging so I can see the command lines and environment to verify that it picks the native one and not the latest one installed.

---

## Comment 11

> Username: jeking3  
> Created_at: 2017-09-15 18:03:02 UTC  
> Url: https://github.com/boostorg/winapi/pull/49#issuecomment-329856026  

I checked by running a msvc-10.0 and msvc-14.1 job with cxxflags="/P" and inspected the resulting .i files.  The msvc-10.0 job was using Platform SDK 7.1A and the msvc-14.1 job was using the latest Windows 10 Kit, so it looks like we're covering a lot of those compile-time branches with the current appveyor configuration.  I removed the build job you suggested, otherwise I think we're good to go in terms of committing this?  I'd like to get it committed so I can move on to fixing the issues I found.

---

## Comment 12

> Username: Lastique  
> Created_at: 2017-09-15 18:06:58 UTC  
> Url: https://github.com/boostorg/winapi/pull/49#issuecomment-329856995  

Thanks again for your work.

---
