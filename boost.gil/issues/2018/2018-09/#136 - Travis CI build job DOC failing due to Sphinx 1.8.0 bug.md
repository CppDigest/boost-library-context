# #136 - Travis CI build job DOC failing due to Sphinx 1.8.0 bug [Closed]

> Username: mloskot  
> Created at: 2018-09-19 09:36:40 UTC  
> Updated at: 2018-09-19 09:40:03 UTC  
> Closed at: 2018-09-19 09:40:03 UTC  
> Url: https://github.com/boostorg/gil/issues/136  

https://travis-ci.org/boostorg/gil/jobs/430177568 is failing with  
  
```  
sphinx-build -b html -d _build/doctrees   . html  
Running Sphinx v1.8.0  
Exception occurred:  
  File "/home/travis/.local/lib/python2.7/site-packages/sphinx/highlighting.py", line 26, in <module>  
    from sphinx.ext import doctest  
SyntaxError: unqualified exec is not allowed in function 'run' it contains a nested function with free variables (doctest.py, line 97)  
The full traceback has been saved in /tmp/sphinx-err-nuw4C0.log, if you want to report the issue to the developers.  
Please also report this if it was a user error, so that a better error message can be provided next time.  
A bug report can be filed in the tracker at <https://github.com/sphinx-doc/sphinx/issues>. Thanks!  
make: *** [html] Error 2  
```  
  
Sphinx bug https://github.com/sphinx-doc/sphinx/issues/5417 was fixed three days ago https://github.com/sphinx-doc/sphinx/pull/5443
