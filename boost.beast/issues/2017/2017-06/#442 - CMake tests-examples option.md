# #442 - CMake tests/examples option [Closed]

> Username: xsacha  
> Created at: 2017-06-08 16:40:46 UTC  
> Updated at: 2017-07-04 02:58:42 UTC  
> Closed at: 2017-07-04 02:58:42 UTC  
> Url: https://github.com/boostorg/beast/issues/442  

By default all tests/examples are compiled. Could these be made in to options?  
See: https://github.com/xsacha/Beast/blob/hunter-b50/CMakeLists.txt  
> option(Beast_BUILD_EXAMPLES "Build examples" OFF)  
> option(Beast_BUILD_TESTS "Build tests" OFF)  
  
Can submit this part as a pull if you want but you probably want them enabled by default?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-08 16:43:26 UTC  
> Url: https://github.com/boostorg/beast/issues/442#issuecomment-307160529  

Hmm...why wouldn't we want to build those?

---

## Comment 2

> Username: xsacha  
> Created at: 2017-06-08 16:44:26 UTC  
> Updated at: 2017-06-08 16:45:50 UTC  
> Url: https://github.com/boostorg/beast/issues/442#issuecomment-307160783  

If we want to include Beast in a submodule or another project, we will not be concerned with tests or examples but just want the linkage and includes.  
  
See example: https://github.com/xsacha/hunter/blob/master/examples/Beast/CMakeLists.txt

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-06-08 16:46:02 UTC  
> Url: https://github.com/boostorg/beast/issues/442#issuecomment-307161177  

If you're using Beast in another project, you don't need Beast's CMakeLists.txt at all, just add an `include_directories` line to your own CMake and that's all you need!

---

## Comment 4

> Username: xsacha  
> Created at: 2017-06-08 16:50:22 UTC  
> Updated at: 2017-06-08 17:00:05 UTC  
> Url: https://github.com/boostorg/beast/issues/442#issuecomment-307162319  

This is only because there is another issue (#443) in the CMakeLists: no target.  
If you have a Beast target, it provides linkage and includes automatically. This is how CMake is designed.  
If you can provide a Beast interface, all my projects can link to the same interface no matter where Beast is located and what it links to.  
  
If I want to release my opensource project with Beast, a target will help ensure my app links to the correct Boost library/headers and Beast headers and not just the first one that is found on the system.  
  
This will also greatly simplify your own CMakeLists. See: your usage of include paths and library linkage vs my commit ( https://github.com/xsacha/Beast/commit/b2c0b427210923496c84599695c38400b98badb3 ).

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-06-08 17:15:02 UTC  
> Url: https://github.com/boostorg/beast/issues/442#issuecomment-307168717  

Your commit is intriguing. I am interested in simplifying the link targets for the tests and examples. However, when using Beast in another project you do not need program_options, coroutine, context, filesystem, or thread - Beast doesn't depend on those (just the tests).  
  
I am also puzzled why you have removed `GroupSources` - that's a very important function because it helps to organize the generated Visual Studio project files which I use exclusively for development.

---

## Comment 6

> Username: xsacha  
> Created at: 2017-06-08 21:09:03 UTC  
> Updated at: 2017-06-09 01:53:28 UTC  
> Url: https://github.com/boostorg/beast/issues/442#issuecomment-307228142  

Misunderstanding on my part. I would change it to only Boost::system in this case. Edit: I have changed to this now, thanks!  
  
I wasn't suggesting going so far yet. Baby steps :) Not all the changes in that commit are suggested for here. Some of them are changes due to unrelated package-management. I probably should have kept GroupSources. Doesn't affect anything and makes VS project look better as you said.  
  
Just these two issues would be enough for me :) That is, optional tests/examples and a target.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2017-07-04 02:58:42 UTC  
> Url: https://github.com/boostorg/beast/issues/442#issuecomment-312768913  

This is now in **develop**
