# #197 Add support for Markdown in doxygen target. [Merged]

> Username: tee3  
> Created at: 2017-05-10 01:29:45 UTC  
> Updated at: 2021-10-02 21:19:24 UTC  
> Merged at: 2017-07-17 22:28:10 UTC  
> Closed at: 2017-07-17 22:28:10 UTC  
> Url: https://github.com/boostorg/build/pull/197  

This change adds support for Markdown in `doxygen`.  
  
* Adds a type `MARKDOWN` for Markdown files, with extension `.markdown` or `.md`.  This change could probably stand on its own as Markdown is a pretty standard file type now.  
  
* Allows Markdown files to be included in the source list for `doxygen` targets.  This should have the effect of using all the Markdown files in the list as input to Doxygen.  The user is responsible for making sure that the Markdown fits in to Doxygen.  
  
This has been manually tested by adding a Markdown `README.md` file to a `doxygen` target in a project, adding `<doxygen:param>USE_MDFILE_AS_MAINPAGE=README.md` to the `requirements` of that `doxygen` target, and verifying that the contents of `README.md` became the `MAINPAGE` of the generated Doxygen documentation.  
  
Note that I did not find tests for other types nor the `doxygen` target, so I did not update any automated tests.  I would gladly add tests for types if anyone has a suggestion on what to do there.  I also would not mind adding a test for the `doxygen` target, but it seems a huge thing to test.

---

## Comment 1

> Username: tee3  
> Created_at: 2017-07-17 22:44:11 UTC  
> Url: https://github.com/boostorg/build/pull/197#issuecomment-315906569  

Thanks Rene!

---
