# #381 - Support for the TAP log format [Open]

> Username: vinipsmaker  
> Created at: 2023-03-30 15:50:29 UTC  
> Updated at: 2023-03-30 15:50:29 UTC  
> Url: https://github.com/boostorg/test/issues/381  

[TAP](https://en.wikipedia.org/wiki/Test_Anything_Protocol) is a simple text-based protocol to communicate the results of unit tests. It's simple, old, and understood by lots of mainstream tools. Among the tools supporting TAP, there is [CMake](https://github.com/Kitware/CMake/commit/3f6ff4b5dba01f2f58dca75546ebefd0830110a5), [Autotools](https://www.gnu.org/software/automake/manual/html_node/Using-the-TAP-test-protocol.html), and [Meson](https://mesonbuild.com/Unit-tests.html#testlogjunitxml).  
  
I'm currently using Meson as the build system and test harness for one of my projects. Meson will produce the JUnit XML file that I upload to the AppVeyor CI, and the results look like this: <https://ci.appveyor.com/project/vinipsmaker/emilua/build/tests>.  
  
Boost.Test also can produce JUnit XML files, but then I'd have multiple XML files and I'd have to take care of them myself (possibly merging results, be careful to not clash with names already used by other test suites, etc).  
  
Fortunately Meson is also a TAP consumer which means that any “unit test” that produces valid TAP output will be properly recognized as its own test suite and Meson will do the proper transformation to include these results in its own JUnit XML file. This is really ideal for my case as not every test is defined by C++ code, and it'd not be trivial to generate XML test output for shell commands, for instance.  
  
So I think it'd be useful for Boost.Test to support the TAP protocol. It'd widen the ecosystem it already supports. Plus the TAP protocol is really simple, so it shouldn't be too difficult to implement.
