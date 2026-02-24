# #1311 - `doxygen_xml2qbk` not found during documentation build [Closed]

> Username: Lastique  
> Created at: 2024-09-24 09:04:30 UTC  
> Updated at: 2024-09-25 09:28:50 UTC  
> Closed at: 2024-09-25 09:28:50 UTC  
> Url: https://github.com/boostorg/geometry/issues/1311  

This is on Kubuntu 24.04, when building Boost.Geometry docs for the first time:  
  
```  
sh: 1: doxygen_xml2qbk: not found  
Boost.Geometry is making .qbk files in /home/lastique/src/boost/libs/geometry/doc  
Boost.Geometry is cleaning Doxygen files in /home/lastique/src/boost/libs/geometry/doc/doxy/doxygen_output/xml/  
Boost.Geometry is cleaning Doxygen files in /home/lastique/src/boost/libs/geometry/doc/index/xml/  
Traceback (most recent call last):  
  File "/home/lastique/src/boost/libs/geometry/doc/make_qbk.py", line 209, in <module>  
    exec(compile(open("make_qbk.py", "rb").read(), "make_qbk.py", 'exec'))  
  File "make_qbk.py", line 38, in <module>  
    run_command(cmd % ("classboost_1_1geometry_1_1index_1_1rtree", "rtree"))  
  File "make_qbk.py", line 27, in run_command  
    raise Exception("Error running %s" % command)  
Exception: Error running doxygen_xml2qbk --xml xml/classboost_1_1geometry_1_1index_1_1rtree.xml --start_include boost/ --output_style alt --alt_max_synopsis_length 59 > generated/rtree.qbk  
  
EXIT STATUS: 1  
====== END OUTPUT ======  
  
      
    PYTHONPATH="/home/lastique/src/boost/libs/geometry/doc"  
export PYTHONPATH  
 "/home/lastique/src/boost/libs/geometry/doc/make_qbk.py" --release-build --doxygen-xml2qbk "../../../bin.v2/libs/geometry/doc/src/docutils/tools/doxygen_xml2qbk/gcc-13/release/x86_64/link-static/threading-multi/visibility-hidden/doxygen_xml2qbk" > "../../../bin.v2/libs/geometry/doc/make_qbk.test/gcc-13/release/x86_64/link-static/threading-multi/visibility-hidden/make_qbk.output" 2>&1 < /dev/null  
    status=$?  
    echo >> "../../../bin.v2/libs/geometry/doc/make_qbk.test/gcc-13/release/x86_64/link-static/threading-multi/visibility-hidden/make_qbk.output"  
    echo EXIT STATUS: $status >> "../../../bin.v2/libs/geometry/doc/make_qbk.test/gcc-13/release/x86_64/link-static/threading-multi/visibility-hidden/make_qbk.output"  
    if test $status -eq 0 ; then  
        cp "../../../bin.v2/libs/geometry/doc/make_qbk.test/gcc-13/release/x86_64/link-static/threading-multi/visibility-hidden/make_qbk.output" "../../../bin.v2/libs/geometry/doc/make_qbk.test/gcc-13/release/x86_64/link-static/threading-multi/visibility-hidden/make_qbk" >/dev/null  
    fi  
    verbose=0  
    if test $status -ne 0 ; then  
        verbose=1  
    fi  
    if test $verbose -eq 1 ; then  
        echo ====== BEGIN OUTPUT ======  
        cat "../../../bin.v2/libs/geometry/doc/make_qbk.test/gcc-13/release/x86_64/link-static/threading-multi/visibility-hidden/make_qbk.output"  
        echo ====== END OUTPUT ======  
    fi  
    exit $status  
  
...failed capture-output ../../../bin.v2/libs/geometry/doc/make_qbk.test/gcc-13/release/x86_64/link-static/threading-multi/visibility-hidden/make_qbk...  
```  
  
[build_log.txt](https://github.com/user-attachments/files/17111546/build_log.txt)  
  
  
This can be worked around by copying `doxygen_xml2qbk`, which was built in this failed run, to a location that is present in `$PATH`. However, this should not be necessary, simply running `b2` should be enough to build the documentation.  
  
What I think happens is, the path to `doxygen_xml2qbk` that is passed to `make_qbk.py` by the `Jamfile` is relative from `libs/geometry/doc`:  
  
```  
"/home/lastique/src/boost/libs/geometry/doc/make_qbk.py" --release-build --doxygen-xml2qbk "../../../bin.v2/libs/geometry/doc/src/docutils/tools/doxygen_xml2qbk/gcc-13/release/x86_64/link-static/threading-multi/visibility-hidden/doxygen_xml2qbk" > "../../../bin.v2/libs/geometry/doc/make_qbk.test/gcc-13/release/x86_64/link-static/threading-multi/visibility-hidden/make_qbk.output" 2>&1 < /dev/null  
```  
  
But `make_qbk.py` performs a `chdir` into `index` [here](https://github.com/boostorg/geometry/blob/6c173505d9e56f0ff0c764204f03022e88f678b5/doc/make_qbk.py#L208), which makes the relative path incorrect for the following `doxygen_xml2qbk` execution.

---

## Comment 1

> Username: barendgehrels  
> Created at: 2024-09-24 16:11:47 UTC  
> Url: https://github.com/boostorg/geometry/issues/1311#issuecomment-2371736585  

@Lastique : Thanks for opening this issue. Indeed, it is inconvenient that doxygen_xml2qbk was not built by b2 itself during the documentation step.  
  
It is a known issue - but good to fix it.  
  
I'm not sure who currently generates documentation per release, but these guy(s) helped us and are aware of this.  
  
Another weird thing is that make_qbk.py calls b2, and vice versa (optionally).  
  
Apart from that, currently, if `doxygen_xml2qbk` is in your path, the documentation generation should work fine, both for the "main" part and for the "index", this is indeed a complicating thing, setup at the start when index was just finished - but there should actually be just one generation step.  
  
I don't dare to touch this procedure as long as I'm not sure who / where creates the release documentation.  
@vissarion you probably know this?

---

## Comment 2

> Username: Lastique  
> Created at: 2024-09-24 16:59:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/1311#issuecomment-2371840942  

Perhaps, a good solution would be to convert the path to `doxygen_xml2qbk` to an absolute path in the `Jamfile` or early in `make_qbk.py` and then always use this full path to invoke it.
