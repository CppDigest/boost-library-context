# #1039 Add bin/ pattern to .gitignore [Closed]

> Username: tinko92  
> Created at: 2022-07-16 20:55:56 UTC  
> Updated at: 2022-08-17 12:26:54 UTC  
> Closed at: 2022-08-17 12:26:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/1039  

This adds a pattern to the .gitignore that matches all the bin/ directories in the main directory or any of the test (or possibly example) subdirectories. They are created when building the tests ~~as instructed by the contribution tutorial~~ within a cloned copy of only BG rather than the full Boost checkout so that they are not listed as modified files by e.g. `git status` or in shell autocomplete for e.g. `git add`.  
  
The motivation for this PR is increased convenience. I might have overlooked a reason against this change but I didn't find anything on the matter in docs/wiki/rejected PRs and couldn't think of anything.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2022-07-16 21:38:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/1039#issuecomment-1186299380  

This probably shouldn't happen.  
  
Are you talking about this section: https://github.com/boostorg/geometry/wiki/Contribution-Tutorial#testing-the-development-branch-of-boostgeometry ?  
  
For me tests and examples are built in `BOOST_ROOT/bin.v2` directory as all other things in Boost. At least when no custom `--build-dir` option is passed to `b2`.  
  
E.g.:  
```  
D:\lib\modular-boost\libs\geometry> ..\..\b2 test  
(...)  
...found 13883 targets...  
...updating 1867 targets...  
compile-c-c++ ..\..\bin.v2\libs\geometry\test\core\core_radian_access.test\msvc-14.1\debug\asynch-exceptions-on\threading-multi\radian_access.obj  
radian_access.cpp  
(...)  
```  
```  
D:\lib\modular-boost\libs\geometry> ..\..\b2 example  
(...)  
...found 7342 targets...  
...updating 63 targets...  
compile-c-c++ ..\..\bin.v2\libs\geometry\example\msvc-14.1\debug\asynch-exceptions-on\threading-multi\c02_custom_box_example.obj  
c02_custom_box_example.cpp  
(...)  
```  
```  
D:\lib\modular-boost\libs\geometry> ..\..\b2 doc\src\examples  
(...)  
...found 6622 targets...  
...updating 391 targets...  
compile-c-c++ ..\..\bin.v2\libs\geometry\doc\src\examples\algorithms\msvc-14.1\debug\asynch-exceptions-on\threading-multi\assign_2d_point.obj  
assign_2d_point.cpp  
(...)  
```

---

## Comment 2

> Username: tinko92  
> Created_at: 2022-07-16 22:44:23 UTC  
> Updated_at: 2022-07-16 22:47:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/1039#issuecomment-1186314771  

It happens when I only clone Boost.Geometry to work on it (I usually do not want the full set of boost libraries which takes a while to clone).  
```  
git clone git@github.com:boostorg/geometry.git  
cd geometry  
b2 cxxflags=-I$(pwd)/include/ test  
```  
(I have a directory with a copy of b2 in PATH)  
If this is bad practice I can close the PR.

---

## Comment 3

> Username: awulkiew  
> Created_at: 2022-07-16 23:26:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/1039#issuecomment-1186329547  

How does it work? Geometry depends on various other Boost libraries:  
https://pdimov.github.io/boostdep-report/boost-1.79.0/geometry.html  
You have to get at least these dependencies if you want to run the tests, if you don't want to clone all of the Boost modules, e.g. like that:  
https://github.com/boostorg/geometry/blob/develop/.circleci/config.yml#L56-L65

---

## Comment 4

> Username: tinko92  
> Created_at: 2022-07-16 23:31:36 UTC  
> Updated_at: 2022-07-16 23:48:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/1039#issuecomment-1186336261  

I always have a current stable installation of boost under /usr/include/boost (installed through my system's package manager) so all dependencies are present in the environment, I only add the include-path for BG because I want the custom branch for the test instead of the BG installed on the system.

---

## Comment 5

> Username: awulkiew  
> Created_at: 2022-07-17 09:27:33 UTC  
> Updated_at: 2022-07-17 12:31:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/1039#issuecomment-1186458110  

Right, so this is different than what is described in the wiki tutorial where Geometry resides inside modular Boost.  
  
For example with your setup you cannot build Geometry together with some specific version of other library (e.g. current develop) to prepare for some change they made which will be released in the next version. Or to check for errors against other libraries because changes in other libraries sometimes interfere with the changes in Geometry. There are also cases when because of our tests we find issues in other libraries and can report them quickly. Maybe you'd like to prepare a fix/PR for these libraries because you probably already debugged it anyway (I did this multiple times). So from practical point of view I think it's better to use the whole modular Boost as Geometry developer.  
  
Not to mention that there are additional dev tools that has to be built, e.g. for checking dependencies and I don't know if they are available in the release.  
  
I do it as it is shown in the wiki, so clone modular boost recursively and inside Geometry add my remote. Of course you need to add the path for the whole modular Boost.  
  
That said I'm not against adding paths to .gitignore if you and others think this is convenient.

---

## Comment 6

> Username: tinko92  
> Created_at: 2022-07-17 22:12:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/1039#issuecomment-1186616448  

> Right, so this is different than what is described in the wiki tutorial where Geometry resides inside modular Boost.  
  
You're right, I was only at the command and didn't regard that the setup is different in the tutorial. I'll correct my initial post.  
  
Thanks for looking into this so swiftly.

---

## Comment 7

> Username: vissarion  
> Created_at: 2022-07-18 13:42:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/1039#issuecomment-1187464664  

I am also using tutorial's workflow that is why I never encounter to that issue. I am not against though. @barendgehrels what do you think?

---

## Comment 8

> Username: barendgehrels  
> Created_at: 2022-07-18 17:30:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/1039#issuecomment-1187868182  

> I am also using tutorial's workflow that is why I never encounter to that issue. I am not against though. @barendgehrels what do you think?  
  
I don’t have that `bin` folder either. But I remember that I had it in my previous setup.  
  
In general I hardly add anything to a project’s `.gitignore`. I’ve seen many versions and everybody put his/her own personal stuff there, therefore I’m not against this one either.  
  
If I create garbage or temporary files myself, I put it in my global `.gitignore` to not bother other people, and I think that should be the first guideline. So I’ve `.vscode` there, `.idea`, things we have here too (not described in the tutorial (but I didn’t check). And all kinds of QtCreator, but I don’t use it anymore. And all kinds of temporary files I tend to create, such as svg files and txt files. But I don’t think these should go to the project’s `.gitnore` file.  
  
Another point is that not everybody is using `b2` and we should also support that workflow. I’m using `CMake` now, it works better with VS Code. I’ve many CMakeLists.txt files, but not all of them. Maybe we should add them if it’s worth sharing. But anyway, another workflow can create another set of binaries or temporaries and it’s a good question whether that should be added to the project’s `.gitignore` or not. But I personally think we should keep it bare.

---

## Comment 9

> Username: barendgehrels  
> Created_at: 2022-07-18 17:36:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/1039#issuecomment-1187892677  

> Right, so this is different than what is described in the wiki tutorial where Geometry resides inside modular Boost.  
  
The tutorial is very good, and I followed it in my latest version. It’s convenient.  
  
But it’s not the only possibility, in my previous setup I had it such that I downloaded various Boost versions and could make symbolic links to the current one. That works more like Tinko has and should be fine too.

---

## Comment 10

> Username: barendgehrels  
> Created_at: 2022-08-17 09:35:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/1039#issuecomment-1217768051  

@tinko92 , it is OK to close it? You can add it into your generic `.gitignore` file anyway.

---

## Comment 11

> Username: tinko92  
> Created_at: 2022-08-17 10:04:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/1039#issuecomment-1217800883  

Yes, that is ok from me, thanks for looking into it.

---
