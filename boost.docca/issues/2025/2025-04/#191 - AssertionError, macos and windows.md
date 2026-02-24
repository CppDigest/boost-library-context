# #191 - AssertionError, macos and windows [Closed]

> Username: sdarwin  
> Created at: 2025-04-09 02:31:10 UTC  
> Updated at: 2025-04-11 11:18:59 UTC  
> Closed at: 2025-04-11 11:18:59 UTC  
> Url: https://github.com/boostorg/docca/issues/191  

Everything's fine on Linux.  
  
"assert member.name not in self.members" ?  
  
boostorg/mysql build on Windows:  
  
```  
"python" "C:\boostorg\boost\tools\docca\docca.py" -i"bin.v2\libs\mysql\doc\_reference-dir\index.xml" -o"bin.v2\libs\mysql\doc\reference.qbk" -c"libs\mysql\doc\config.json" -c"bin.v2\libs\mysql\doc\_reference-dir\docca-config.json"  -T"C:\boostorg\boost\tools\docca\include\docca\quickbook.jinja2" -I"." -I"C:\boostorg\boost\libs\mysql\include"  
  
Traceback (most recent call last):  
  File "C:\boostorg\boost\tools\docca\docca.py", line 1387, in <module>  
    main(sys.argv, sys.stdin, sys.stdout, os.path.realpath(__file__))  
  File "C:\boostorg\boost\tools\docca\docca.py", line 1367, in main  
    data = collect_data(data_dir, refs)  
  File "C:\boostorg\boost\tools\docca\docca.py", line 1253, in collect_data  
    factory(element, result)  
  File "C:\boostorg\boost\tools\docca\docca.py", line 809, in __init__  
    super().__init__(element, None, index)  
  File "C:\boostorg\boost\tools\docca\docca.py", line 768, in __init__  
    assert member.name not in self.members  
AssertionError  
...failed docca.generate-reference bin.v2\libs\mysql\doc\reference.qbk...  
...skipped <pbin.v2\libs\mysql\doc>mysql_doc.xml for lack of <pbin.v2\libs\mysql\doc-object(qbk-scanner)@17506>reference.qbk...  
...skipped <pbin.v2\libs\mysql\doc>mysql_doc.docbook for lack of <pbin.v2\libs\mysql\doc-object(xinclude-scanner)@17505>mysql_doc.xml...  
...skipped <pbin.v2\libs\mysql\doc>mysql_HTML.manifest for lack of <pbin.v2\libs\mysql\doc>mysql_doc.docbook...  
```  
  
boostorg/json build on Windows:  
  
```  
  "python" "C:\boostorg\boost\tools\docca\docca.py" -i"bin.v2\libs\json\doc\_reference-dir\index.xml" -o"bin.v2\libs\json\doc\reference.qbk" -c"libs\json\doc\config.json" -c"bin.v2\libs\json\doc\_reference-dir\docca-config.json"  -T"C:\boostorg\boost\tools\docca\include\docca\quickbook.jinja2" -I"."  
  
Traceback (most recent call last):  
  File "C:\boostorg\boost\tools\docca\docca.py", line 1387, in <module>  
    main(sys.argv, sys.stdin, sys.stdout, os.path.realpath(__file__))  
  File "C:\boostorg\boost\tools\docca\docca.py", line 1367, in main  
    data = collect_data(data_dir, refs)  
  File "C:\boostorg\boost\tools\docca\docca.py", line 1253, in collect_data  
    factory(element, result)  
  File "C:\boostorg\boost\tools\docca\docca.py", line 809, in __init__  
    super().__init__(element, None, index)  
  File "C:\boostorg\boost\tools\docca\docca.py", line 768, in __init__  
    assert member.name not in self.members  
AssertionError  
...failed docca.generate-reference bin.v2\libs\json\doc\reference.qbk...  
...skipped <pbin.v2\libs\json\doc>json_doc.xml for lack of <pbin.v2\libs\json\doc-object(qbk-scanner)@17551>reference.qbk...  
...skipped <pbin.v2\libs\json\doc>json_doc.docbook for lack of <pbin.v2\libs\json\doc-object(xinclude-scanner)@17550>json_doc.xml...  
...skipped <pbin.v2\libs\json\doc>json_HTML.manifest for lack of <pbin.v2\libs\json\doc>json_doc.docbook...  
```  
  
boostorg/json build on  macos:  
  
```  
docca.generate-reference bin.v2/libs/json/doc/reference.qbk  
Traceback (most recent call last):  
  File "/Users/runner/work/release-tools/release-tools/github/boostorg/boost/tools/docca/docca.py", line 1387, in <module>  
    main(sys.argv, sys.stdin, sys.stdout, os.path.realpath(__file__))  
    ~~~~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^  
  File "/Users/runner/work/release-tools/release-tools/github/boostorg/boost/tools/docca/docca.py", line 1367, in main  
    data = collect_data(data_dir, refs)  
  File "/Users/runner/work/release-tools/release-tools/github/boostorg/boost/tools/docca/docca.py", line 1253, in collect_data  
    factory(element, result)  
    ~~~~~~~^^^^^^^^^^^^^^^^^  
  File "/Users/runner/work/release-tools/release-tools/github/boostorg/boost/tools/docca/docca.py", line 809, in __init__  
    super().__init__(element, None, index)  
    ~~~~~~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^  
  File "/Users/runner/work/release-tools/release-tools/github/boostorg/boost/tools/docca/docca.py", line 768, in __init__  
    assert member.name not in self.members  
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^  
AssertionError  
  
    "/Users/runner/venvboostdocs/bin/python" "/Users/runner/work/release-tools/release-tools/github/boostorg/boost/tools/docca/docca.py" -i"bin.v2/libs/json/doc/_reference-dir/index.xml" -o"bin.v2/libs/json/doc/reference.qbk" -c"libs/json/doc/config.json" -c"bin.v2/libs/json/doc/_reference-dir/docca-config.json"  -T"/Users/runner/work/release-tools/release-tools/github/boostorg/boost/tools/docca/include/docca/quickbook.jinja2" -I"."  
  
...failed docca.generate-reference bin.v2/libs/json/doc/reference.qbk...  
```

---

## Comment 1

> Username: grisumbras  
> Created at: 2025-04-09 10:57:22 UTC  
> Url: https://github.com/boostorg/docca/issues/191#issuecomment-2789273697  

I suspect this is due to Python version differences or Doxygen version differences. Can you give me those versions?

---

## Comment 2

> Username: sdarwin  
> Created at: 2025-04-09 11:12:07 UTC  
> Url: https://github.com/boostorg/docca/issues/191#issuecomment-2789327769  

Linux:   
doxygen fixed at Release_1_9_5  
python 3.12  
	  
Windows:   
doxygen 1.13.2 (latest)  
python 3.9.13  
	  
Macos:   
doxygen 1.13.2 (latest)  
python 3.13

---

## Comment 3

> Username: grisumbras  
> Created at: 2025-04-09 11:22:12 UTC  
> Url: https://github.com/boostorg/docca/issues/191#issuecomment-2789352543  

Aha, that is very likely Doxygen differences.

---

## Comment 4

> Username: grisumbras  
> Created at: 2025-04-09 11:58:15 UTC  
> Url: https://github.com/boostorg/docca/issues/191#issuecomment-2789458823  

Can confirm, this is due to a change in Doxygen.

---

## Comment 5

> Username: grisumbras  
> Created at: 2025-04-09 14:56:36 UTC  
> Url: https://github.com/boostorg/docca/issues/191#issuecomment-2790022114  

The culprit appears to be https://github.com/doxygen/doxygen/commit/ef4065af3fb3b6a73b6d919a05f02a170880fefe. I'm trying to figure out how to work around the problematic behaviour (which certainly looks like a Doxygen bug to me).

---

## Comment 6

> Username: grisumbras  
> Created at: 2025-04-11 07:56:43 UTC  
> Url: https://github.com/boostorg/docca/issues/191#issuecomment-2796138806  

https://github.com/doxygen/doxygen/issues/11541
