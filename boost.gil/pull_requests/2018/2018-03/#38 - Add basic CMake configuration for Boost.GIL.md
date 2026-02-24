# #38 Add basic CMake configuration for Boost.GIL [Merged]

> Username: mloskot  
> Created at: 2018-03-11 22:59:46 UTC  
> Updated at: 2018-03-21 12:53:07 UTC  
> Merged at: 2018-03-21 12:46:19 UTC  
> Closed at: 2018-03-21 12:46:19 UTC  
> Url: https://github.com/boostorg/gil/pull/38  

Add basic CMake configuration for Boost.GIL  
  
First stab at collection of `CMakeLists.txt` for Boost.GIL  
- Allow building and testing boostorg/gil against Boost from cloned superproject or installed distribution.  
  
Add `CMakeSettings.json` config file for VS2017 integration with CMake  
- Defines build configurations for VS and Ninja generators.  
- Can be used as is or as a template ready to customise.  
  
Add `conanfile.txt `for Conan package manager (eg. for `cmake -DGIL_USE_CONAN=ON`).  
Add `.editignore` file to with basic encoding of CMake and CI scripts.  
Update `.appveyor.yml` with two extra CMake-based builds (allowed to fail).  
  
-----  
## Motivation  
  
Provide CMake build configuration for convenient development, building and debugging using CMake-based IDEs.  
  
Allow building and testing Boost.GIL against staged build of Boost as well as in lightweight scenario where only `boostorg/gil` repository is cloned and tested against pre-preinstalled Boost binaries:  
  
1. Install Boost from released distribution or clone the Boost superproject (see [Boost Wiki](https://github.com/boostorg/boost/wiki/Getting-Started)).  
2. `git clone https://github.com/boostorg/gil.git`  
3. `cd gil`  
4. `mkdir _build && cd  _build`  
5. `cmake -DBOOST_ROOT=/path/to/boost/aimed/to/target ..`  
  
## Tasklist  
  
 - [x] Add `CMakeLists.txt` for core tests  
 - [x] Add `CMakeLists.txt` for numeric tests  
 - [x] Add `CMakeLists.txt` for toolbox tests  
 - [x] Add `CMakeLists.txt` for IO tests, including look-up for libjpeg, libpng, etc. dependencies (allow use of Conan and Vcpkg).  
 - [x] Enable building tests against Boost  
        - from stage (implicit `BOOST_ROOT` points to root of current source tree)  
        - installed Boost (eg. `C:\local\boost_1_66_0`) found without `BOOST_ROOT` specified  
        - Boost found in specified `BOOST_ROOT` location  
 - [x] Add CMake-based build to CI (eg. AppVeyor or new CircleCI)  
 - [ ] Review  
 - [ ] Adjust for comments

---

## Comment 1

> Username: chhenning  
> Created_at: 2018-03-20 22:13:32 UTC  
> Updated_at: 2018-03-20 22:13:43 UTC  
> Url: https://github.com/boostorg/gil/pull/38#issuecomment-374775161  

@mloskot Can we merge this?

---

## Comment 2

> Username: chhenning  
> Created_at: 2018-03-20 22:15:18 UTC  
> Url: https://github.com/boostorg/gil/pull/38#issuecomment-374775594  

Can we have a cmake for setting up tests for gil developer? Such person would be interested in running the complete test suite with all test images available in a folder.

---

## Comment 3

> Username: mloskot  
> Created_at: 2018-03-20 22:40:23 UTC  
> Updated_at: 2018-03-20 22:40:32 UTC  
> Url: https://github.com/boostorg/gil/pull/38#issuecomment-374781734  

@chhenning Do you approve it? If you do, then from my point of view, it's ready to be merged.

---

## Comment 4

> Username: mloskot  
> Created_at: 2018-03-20 22:44:11 UTC  
> Updated_at: 2018-03-20 22:45:02 UTC  
> Url: https://github.com/boostorg/gil/pull/38#issuecomment-374782569  

@chhenning   
> Can we have a cmake for setting up tests for gil developer?  
  
I'm not sure I understand what you mean.  
Do you ask about reproducing this `test-suite full` target, https://github.com/boostorg/gil/blob/develop/io/test/Jamfile#L47-L95, in CMake configuration? It is possible, why not. I can add it later.

---

## Comment 5

> Username: chhenning  
> Created_at: 2018-03-21 00:02:12 UTC  
> Updated_at: 2018-03-21 00:02:40 UTC  
> Url: https://github.com/boostorg/gil/pull/38#issuecomment-374798108  

Oh, yes the test-suit full target is what I have in mind.   
  
It would be very cool to have cmake create the development environment (visual studio project for me) so I can add new code. Such environment should add all gil headers, including the extensions. Is that possible?

---

## Comment 6

> Username: mloskot  
> Created_at: 2018-03-21 09:28:17 UTC  
> Url: https://github.com/boostorg/gil/pull/38#issuecomment-374877138  

@chhenning   
> test-suit full target is what I have in mind  
  
I see no problem to add it, when I have time.  
  
> cmake create the development environment (visual studio project for me) so I can add new code  
> Such environment should add all gil headers, including the extensions. Is that possible?  
  
Simply, try the setup proposed in this PR :-)  
  
For Visual Studio 2017 specifically, learn about and try out its integration for CMake, https://blogs.msdn.microsoft.com/vcblog/2016/10/05/cmake-support-in-visual-studio/. Then, try this with the setup from this PR. Finally, let's discuss what you think is missing (apart from the `test-suite full` target, what we already discussed).

---

## Comment 7

> Username: mloskot  
> Created_at: 2018-03-21 12:53:07 UTC  
> Url: https://github.com/boostorg/gil/pull/38#issuecomment-374926248  

@chhenning Thanks for accepting.

---
