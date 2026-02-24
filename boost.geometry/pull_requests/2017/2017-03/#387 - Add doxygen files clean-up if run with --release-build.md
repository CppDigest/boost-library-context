# #387 [doc] Add doxygen files clean-up if run with --release-build [Merged]

> Username: mloskot  
> Created at: 2017-03-28 00:17:06 UTC  
> Updated at: 2018-10-12 09:51:06 UTC  
> Merged at: 2017-03-28 20:14:16 UTC  
> Closed at: 2017-03-28 20:14:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/387  

Delete Doxygen-generated files to avoid copying them into release archive.  
Replace os.remove with shutil.rmtree to cope with sub-directories.  
  
------  
  
Boost 1.64.0 Beta1 source archive should not include  `html_by_doxygen` or `xml`.  
It was discussed here http://lists.boost.org/geometry/2017/03/3618.php  
  
I tried to follow Rene Rivera suggestions, http://lists.boost.org/Archives/boost/2017/03/233878.php, and regenerate release process to generate source archive w/o any Doxygen intermediate files generated in `libs/geometry/docs`. Hopefully, this will work for Rene.  
  
If accepted and merged into `develop`, it should also be merged into `master` according to Rene's info http://lists.boost.org/Archives/boost/2017/03/233810.php  
  
If there is anything I can do, let me know.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2017-03-28 10:07:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/387#issuecomment-289723975  

I'm not sure why but while running it without `--release-build` the directories are removed anyway.

---

## Comment 2

> Username: mloskot  
> Created_at: 2017-03-28 10:27:36 UTC  
> Updated_at: 2017-03-28 10:28:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/387#issuecomment-289728473  

Weird. The clean-up is guarded with `if "--release-build" in sys.argv:`  
  
The only unguarded clean-up is at the beginning of the process in `call_doxygen` and top of `index/make_qbk.py`. I can't check it now, will try later tonight.  
  
IMHO, Rene needs fix ASAP and I think clean release package is more important and development workflow could be fixed any time independently from releasing 1.64.0.

---

## Comment 3

> Username: awulkiew  
> Created_at: 2017-03-28 15:17:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/387#issuecomment-289803899  

I run `make_qbk.py` manually without `--release-build` "as usual". So at the end of the `make_qbk.py` script `b2` is executed inside `doc` directory and `b2` then runs the `make_qbk.py` again but this time with `--release-build`.  
  
Here are relevant changes: https://github.com/boostorg/geometry/commit/f13b300eb4ac17dce0bb84cff3b9b461397158f3 https://github.com/boostorg/geometry/commit/8e9835ceccb1e7b64b5e53bfb7803651221a5648  
  
So it seems that for the release the docs are built with `b2` directly which executes `make_qbk.py` script. However this way it's impossible to NOT pass `--release-build` because it's hardcoded into the `Jamfile.v2`.

---

## Comment 4

> Username: mloskot  
> Created_at: 2017-03-28 15:59:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/387#issuecomment-289817718  

Your explanation is clear to me, but I'm observing different results:  
  
* Ensure clean slate  
  
```  
~/boost/libs/geometry/doc$ rm -rf index/xml/  
~/boost/libs/geometry/doc$ rm -rf index/html_by_doxygen/  
~/boost/libs/geometry/doc$ rm -rf doxy/doxygen_output/xml/  
~/boost/libs/geometry/doc$ rm -rf doxy/doxygen_output/html_by_doxygen/  
~/boost/libs/geometry/doc$   
```  
  
* Run `make_qbk.py` manually  
  
```  
~/boost/libs/geometry/doc$ python2 ./make_qbk.py   
Boost.Geometry is making .qbk files in /home/mloskot/boost/libs/geometry/doc  
...  
quickbook.quickbook-to-boostbook ../../../bin.v2/libs/geometry/doc/gcc-5.4.0/debug/geometry.boostbook  
Generating Output File: ../../../bin.v2/libs/geometry/doc/gcc-5.4.0/debug/geometry.boostbook  
...updated 1 target...  
~/boost/libs/geometry/doc$   
```  
  
* Check if doxygen-generated are still there  
  
```  
~/boost/libs/geometry/doc$ ls -1 index/xml/ | wc -l  
61  
~/boost/libs/geometry/doc$ ls -1 index/html_by_doxygen/ | wc -l  
87  
~/boost/libs/geometry/doc$ ls -1 doxy/doxygen_output/html_by_doxygen/ | wc -l  
2430  
~/boost/libs/geometry/doc$ ls -1 doxy/doxygen_output/xml/ | wc -l  
1449  
~/boost/libs/geometry/doc$   
```  
  
Now, if I make `make_qbk.py` scripts more verbose about the `sys.argv`:  
  
```  
diff --git a/doc/index/make_qbk.py b/doc/index/make_qbk.py  
index 64775b1bf..07dccecdc 100755  
--- a/doc/index/make_qbk.py  
+++ b/doc/index/make_qbk.py  
@@ -18,6 +18,7 @@ cmd = cmd + " --output_style alt"  
 cmd = cmd + " > generated/%s.qbk"  
  
 def run_command(command):  
+    print('index/make_qbk args: %s' % str(sys.argv))  
     if os.system(command) != 0:  
         raise Exception("Error running %s" % command)  
  
diff --git a/doc/make_qbk.py b/doc/make_qbk.py  
index cace58ff8..88c38bdcf 100755  
--- a/doc/make_qbk.py  
+++ b/doc/make_qbk.py  
@@ -199,5 +199,6 @@ if "--release-build" in sys.argv:  
     remove_all_files("index/html_by_doxygen/")  
  
 # Use either bjam or b2 or ../../../b2 (the last should be done on Release branch)  
+print('make_qbk args: %s' % str(sys.argv))  
 if "--release-build" not in sys.argv:  
     run_command("b2")  
```  
  
* I can see no `--release-build` arg is passed,   
  
```  
~/boost/libs/geometry/doc$ python2 ./make_qbk.py   
Boost.Geometry is making .qbk files in /home/mloskot/dev/boost-release-tools/boostorg/boost/libs/geometry/doc  
Boost.Geometry is cleaning Doxygen files in /home/mloskot/dev/boost-release-tools/boostorg/boost/libs/geometry/doc/doxy/doxygen_output/xml/  
performance:-1: warning: multiple use of section label 'performance', (first occurrence: /home/mloskot/dev/boost-release-tools/boostorg/boost/libs/geometry/doc/doxy/doxygen_input/pages/doxygen_pages.hpp, line 152)  
Boost.Geometry is cleaning Doxygen files in /home/mloskot/dev/boost-release-tools/boostorg/boost/libs/geometry/doc/index/xml/  
index/make_qbk args: ['./make_qbk.py']  
index/make_qbk args: ['./make_qbk.py']  
index/make_qbk args: ['./make_qbk.py']  
index/make_qbk args: ['./make_qbk.py']  
index/make_qbk args: ['./make_qbk.py']  
index/make_qbk args: ['./make_qbk.py']  
index/make_qbk args: ['./make_qbk.py']  
index/make_qbk args: ['./make_qbk.py']  
index/make_qbk args: ['./make_qbk.py']  
index/make_qbk args: ['./make_qbk.py']  
index/make_qbk args: ['./make_qbk.py']  
index/make_qbk args: ['./make_qbk.py']  
index/make_qbk args: ['./make_qbk.py']  
index/make_qbk args: ['./make_qbk.py']  
make_qbk args: ['./make_qbk.py']  
index/make_qbk args: ['./make_qbk.py']  
...  
Generating Output File: ../../../bin.v2/libs/geometry/doc/gcc-5.4.0/debug/geometry.boostbook  
...updated 3 targets...  
mloskot@vb-ubuntu1604:~/dev/boost-release-tools/boostorg/boost/libs/geometry/doc$  
```  
  
Notice the log above indicates the second run is cleaning previously generated stuff, which was not cleaned automatically:  
  
```  
Boost.Geometry is cleaning Doxygen files in /home/mloskot/boost/libs/geometry/doc/doxy/doxygen_output/xml/  
Boost.Geometry is cleaning Doxygen files in /home/mloskot/dev/boost/libs/geometry/doc/index/xml/  
```  
  
Unless I'm still missing something, which I may of course, AFAICT, during manual run, no `--release-build` is passed and the patch works as expected.

---

## Comment 5

> Username: awulkiew  
> Created_at: 2017-03-28 19:38:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/387#issuecomment-289881193  

I updated Boost, rebuilt b2, headers, etc. and it seems that this fixed this issue for me. Sorry for causing problems.  
  
TL;DR  
  
> Notice the log above indicates the second run is cleaning previously generated stuff  
  
This is not the second run, it's the first one:  
`Boost.Geometry is making .qbk files ... ` is printed here:  
https://github.com/mloskot/geometry/blob/7e08464fffd1f40299e5f6c19384684d58372f5c/doc/make_qbk.py#L18  
`Boost.Geometry is cleaning Doxygen files ... doc/doxy/doxygen_output/xml/` is printed here (in `call_doxygen` -> `remove_all_files`):  
https://github.com/mloskot/geometry/blob/7e08464fffd1f40299e5f6c19384684d58372f5c/doc/make_qbk.py#L90  
`Boost.Geometry is cleaning Doxygen files ... doc/index/xml/` is printed here: (in `remove_all_files`): https://github.com/mloskot/geometry/blob/7e08464fffd1f40299e5f6c19384684d58372f5c/doc/index/make_qbk.py#L30  
  
Then `b2` is executed. And when the `make_qbk.py` script is executed by `b2` it doesn't print anything. To check this instead of running `make_qbk_py` run `b2`. If printing worked it'd at least print `Boost.Geometry is making .qbk files ... ` but it doesn't. Still the documentation is generated.

---

## Comment 6

> Username: mloskot  
> Created_at: 2017-03-28 19:50:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/387#issuecomment-289884003  

I mean it is second run I do, run of the chain of `make_qbk.py` followed by `index/make_qbk.py`.  
  
My first run is the bullet "Run make_qbk.py manually".  
  
Nothing is cleaned.  
  
Then. the second run enters and starts with cleaning  
  
`Boost.Geometry is cleaning Doxygen files`

---

## Review 7 [Approved]

> Username: awulkiew  
> Created_at: 2017-03-28 20:05:21 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/387#pullrequestreview-29560050  

Thanks! I'm ok with merging this PR. Below are suggestions which you could implement but that's not a requirement.  
  
Instead of clearing out the `index` dir in the `index/make_qbk.py` and doing the same in the `make_qbk.py`, `index/make_qbk.py` could be executed with `--release-build` option and cleanup related to the index could be done only there.  
  
There are generated `*.qbk` files in directories `doc/generated` and `doc/index/generated` which are not needed. Also `doc/html/geometry_HTML.manifest` file. Ideally they probably should be removed as well, but this should be done after `b2` is called. But since `b2` can run `make_qbk.py` and the script is then executed before quickbook it is impossible to implement the cleanup of generated qbk files in this single script. Maybe it could be done in a separate cleanup script run by `b2` after building docs from qbk files, but I don't know how exactly.

---

## Comment 8

> Username: mloskot  
> Created_at: 2017-03-28 20:26:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/387#issuecomment-289893818  

@awulkiew Could you or someone merge this into the master?  
  
I think this PR falls into the **Documentation fixes and other minor changes not affecting code** category of the https://github.com/boostorg/boost/wiki/Release-Beta-Merge-Policy  
  
p.s. I am about to go offline for some days/week(s), so I won't be able to help or test anything related to this PR. Once I'm back, I'll continue with #388

---

## Comment 9

> Username: awulkiew  
> Created_at: 2017-03-28 22:08:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/387#issuecomment-289920078  

@mloskot thanks!  
@barendgehrels will be merging into master soon so I think he'll merge this one as well.

---

## Comment 10

> Username: barendgehrels  
> Created_at: 2017-03-29 08:46:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/387#issuecomment-290024627  

@mloskot , @awulkiew , sure, I will. Thanks!

---
