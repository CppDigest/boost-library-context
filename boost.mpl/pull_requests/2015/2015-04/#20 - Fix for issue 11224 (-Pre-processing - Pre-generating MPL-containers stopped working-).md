# #20 Fix for issue 11224 ("Pre-processing / Pre-generating MPL-containers stopped working") [Merged]

> Username: DenizThatMenace  
> Created at: 2015-04-22 12:06:41 UTC  
> Updated at: 2015-05-19 10:20:00 UTC  
> Merged at: 2015-05-18 14:42:49 UTC  
> Closed at: 2015-05-18 14:42:49 UTC  
> Url: https://github.com/boostorg/mpl/pull/20  

This fixes problems with pre-processing MPL-containers.  
- References to non-supported compilers are removed.  
- Added a python-script "fix_boost_mpl_preprocess.py" which fixes the header-comments (required during pre-processing), that no longer get automatically generated due to the migration from Subversion to Git.  
- Added a helper python-script "boost_mpl_preprocess.py" which very much simplifies running the pre-processing steps.

---

## Comment 1

> Username: eldiener  
> Created_at: 2015-04-28 23:38:58 UTC  
> Url: https://github.com/boostorg/mpl/pull/20#issuecomment-97264237  

I am looking at this but I need to be able to understand the issue completely before merging. I do see where the svn entries are completely blank under git, which necessitates different python scripts for generating the pre-processed header files.

---

## Comment 2

> Username: eldiener  
> Created_at: 2015-04-29 03:32:30 UTC  
> Url: https://github.com/boostorg/mpl/pull/20#issuecomment-97294488  

I would like to see this fix with fix_boost_mpl_preprocess.py being automatically invoked when needed, when boost_mpl_preprocess.py is invoked. The code could check if the '$Id$' string in the first source file found is empty and if so then run fix_boost_mpl_preprocess.py before finishing up boost_mpl_preprocess.py. Since fix_boost_mpl_preprocess.py will need to be run before boost_mpl_preprocess.py on Boost 1.56 on up this will relieve the end-user of manually having to run fix_boost_mpl_preprocess.py in most cases before regenerating the pre-processing MPL-containers when he wants a greater number of elements.  
  
Ideally it would be best if the whole reliance on '$Id$' did not exist but I haven't studied the original pre-processing MPL container code to understand why that reliance is needed. I do suspect that there is probably a better way.

---

## Comment 3

> Username: DenizThatMenace  
> Created_at: 2015-05-01 09:47:47 UTC  
> Url: https://github.com/boostorg/mpl/pull/20#issuecomment-98086479  

**To help you understand the issue:**  
  
The original python-scripts (`preprocess.py` and `preprocess_*.py`) use _GCC_ to (CPP-)preprocess source-files (using the _GCC_-command from `preprocess.cmd`). This results in long source-files that - among other things - have all include-directives replaced by the content of the included header-files. Comments are preserved explicitly.  
These long source-files are then processed by the python-script `pp.py` which somehow needs to find the start of the original header-file (e.g. `vector50.hpp`) that shall be pre-generated. Currently, this is done by finding the identifier in the header-comment including its name (e.g. `$Id: vector50.hpp 2015-05-01$`). Then pre-generation can go on and succeed from there.  
  
If there would be another way to determine the start of the header file that would be fine, too. However, currently I can only think of directly putting the file's name into the header-comment and then committing it to _Git_. But that is error-prone (if some file-names might change later) and also not much clearer.

---

## Comment 4

> Username: DenizThatMenace  
> Created_at: 2015-05-01 09:55:55 UTC  
> Url: https://github.com/boostorg/mpl/pull/20#issuecomment-98087167  

I agree with you that if no reliance on `$Id$` would exist it would be best, but currently I do not know a better solution. For a quick fix I would suggest staying with `$Id$` and just getting it to work again.  
  
Originally I thought about the fix being automatically applied every time the `boost_mpl_preprocess.py` is called, but at the end I put the fix in a separate file instead. But you are right, it probably is the best solution to have it as a separate file but allow the `boost_mpl_preprocess.py` script to call it prior to pre-generation if the first encountered file has the wrong format.  
  
I could try to modify the script to incorporate that change.

---

## Comment 5

> Username: eldiener  
> Created_at: 2015-05-01 10:25:14 UTC  
> Url: https://github.com/boostorg/mpl/pull/20#issuecomment-98094991  

Thanks for the explanation about how the current process is supposed to work.  
  
Please try to modify boost_mpl_preprocess.py to call  fix_boost_mpl_preprocess.py automatically if necessary. It should be fairly straightforward: just test the first file found to see if $Id$ is empty and, if it is, invoke fix_boost_mpl_preprocess.py before proceeding to the rest of boost_mpl_preprocess.py.  
  
If you can do that I will also test it and then merge the pull request. I agree with you that keeping the $Id$ for now is the best solution until we can find a better one if a better one exists.

---

## Comment 6

> Username: DenizThatMenace  
> Created_at: 2015-05-01 16:12:20 UTC  
> Url: https://github.com/boostorg/mpl/pull/20#issuecomment-98168088  

I did the changes we were talking about. Thereby I also fixed some small errors, which I somehow might have overlooked in my original commit.  
  
Please test it and tell me if you need some further modifications before accepting this pull request.

---

## Comment 7

> Username: eldiener  
> Created_at: 2015-05-18 14:42:34 UTC  
> Url: https://github.com/boostorg/mpl/pull/20#issuecomment-103082662  

I am going to accept the pull request and put it on 'develop'. I have tested it and it works in generating the needed files without error, and in passing the already created test for MPL.  
  
Here are some changes I would like to see, but which I do not deem absolute necessary to at least get this to testing on develop.  
  
1) A default source directory for boost_mpl_preprocess.py processing the could be the top-level Boost directory from where the boost_mpl_preprocess.py exists when it is invoked. You should be able to figure this out from the python script itself by looking at the current directory. If it has a boost_mpl_process.py in it and a source directory is not specified, then you should be able to work back from the current directory to the top-level Boost directory. This would allow the end-user to simply invoke boost_mpl_process.py from the directory where it resides, without having to specify a Boost source directory, and have it work automatically.  
  
2) We really need tests which will show that more than 50 elements are processed correctly when we expand the number of elements beyond the original 50 being distributed. I don't even think that MPL currently has tests of around 50 elements, which is what the distribution creates automatically.  
  
I appreciate all the work you have done to get this change into MPL and to automate this process correctly again.

---

## Comment 8

> Username: DenizThatMenace  
> Created_at: 2015-05-19 10:20:00 UTC  
> Url: https://github.com/boostorg/mpl/pull/20#issuecomment-103428353  

I implemented your suggestion 1) and already provided another pull-request (#21) for it.

---
