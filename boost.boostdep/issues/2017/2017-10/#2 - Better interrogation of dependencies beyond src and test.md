# #2 - Better interrogation of dependencies beyond src and test [Closed]

> Username: jeking3  
> Created at: 2017-10-22 11:42:04 UTC  
> Updated at: 2017-10-25 02:59:55 UTC  
> Closed at: 2017-10-25 02:59:55 UTC  
> Url: https://github.com/boostorg/boostdep/issues/2  

Some libraries have benchmark, example, and tool directories.  Currently the python method to scan for dependencies does not look for either.  Instead perhaps, could the tool simply scan for all source code files in the entire project directory?  
  
When I added a "tool" directory with program source that uses boost::program_options into Boost.Format, boostdep failed to pick that up.  
  
Ideally if one could ask bjam for a list of all sources within a project, that would be even better.

---

## Comment 1

> Username: pdimov  
> Created at: 2017-10-22 16:43:03 UTC  
> Url: https://github.com/boostorg/boostdep/issues/2#issuecomment-338491245  

Yes, in principle. I added `example` recently in addition to `test` because many libraries build the examples as a part of the test suite but there should probably be a mechanism for controlling which directories are scanned.  
  
In this specific case however, do you actually need the dependencies of the `tool` directory when running the tests?

---

## Comment 2

> Username: jeking3  
> Created at: 2017-10-22 20:39:16 UTC  
> Url: https://github.com/boostorg/boostdep/issues/2#issuecomment-338507344  

Recently I found the format/benchmark had rotted because it wasn't built regularly, so I want the format/benchmark and format/tools directories to build properly.  What I am doing right now is that as part of the CI build I copy the sources from tools into test, so the deps tool picks it up.  It works, but it would be nice if the deps tool could ask bjam for a list of source code files in the project?

---

## Comment 3

> Username: pdimov  
> Created at: 2017-10-22 21:06:33 UTC  
> Url: https://github.com/boostorg/boostdep/issues/2#issuecomment-338509139  

That's probably not possible in the general case, because the point of `depinst.py` is to determine what to install, but until everything necessary is installed, `b2 test` will generally error out due to not finding the not-installed parts.  
  
I should just add an option through which one would pass additional directories to scan.

---

## Comment 4

> Username: jeking3  
> Created at: 2017-10-22 21:21:47 UTC  
> Url: https://github.com/boostorg/boostdep/issues/2#issuecomment-338510162  

Or parse the top level Jamfile which is typically just including subdirectories, but yes, allowing for additional directory names would work for me in this case.  Thanks!

---

## Comment 5

> Username: pdimov  
> Created at: 2017-10-25 02:59:55 UTC  
> Url: https://github.com/boostorg/boostdep/issues/2#issuecomment-339199209  

Added support for `-I this -I that` in https://github.com/boostorg/boostdep/commit/e27e853d9961e95cd8f07277804c61b5200bad25.
