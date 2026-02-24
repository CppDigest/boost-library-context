# #345 - Sphinx 1.7.9 is failing on Travis CI [Closed]

> Username: mloskot  
> Created at: 2019-07-21 22:46:10 UTC  
> Updated at: 2019-07-21 23:50:14 UTC  
> Closed at: 2019-07-21 23:49:47 UTC  
> Url: https://github.com/boostorg/gil/issues/345  

https://github.com/boostorg/gil/blob/24468697f5df5feccdf3421f7a57c964e9e4c150/.travis.yml#L196  
  
@simmplecoder reported on Gitter that documentation build started failing on Travis CI, despite nothing has changed in our Sphinx or Boost.Build configuration related to docs build.  
  
It happened for his PR #342, during docs build https://travis-ci.org/boostorg/gil/jobs/561815148  
  
```  
sphinx-build -b html -d _build/doctrees   . html  
Traceback (most recent call last):  
  File "/home/travis/.local/bin/sphinx-build", line 10, in <module>  
    sys.exit(main())  
  File "/home/travis/.local/lib/python2.7/site-packages/sphinx/cmd/build.py", line 38, in main  
    return build_main(argv)  
  File "/home/travis/.local/lib/python2.7/site-packages/sphinx/cmd/build.py", line 22, in build_main  
    from sphinx import cmdline  
  File "/home/travis/.local/lib/python2.7/site-packages/sphinx/cmdline.py", line 19, in <module>  
    from docutils.utils import SystemMessage  
  File "/home/travis/.local/lib/python2.7/site-packages/docutils/utils/__init__.py", line 21, in <module>  
    import docutils.io  
  File "/home/travis/.local/lib/python2.7/site-packages/docutils/io.py", line 348  
    (self.destination.mode, mode)), file=self._stderr)  
                                        ^  
SyntaxError: invalid syntax  
```  
  
I noticed the same issue for my PR #344: https://travis-ci.org/boostorg/gil/builds/561818727  
  
------  
  
To investigate the problem, I first added dumping of `pip` and `sphinx-build` version in https://github.com/boostorg/gil/commit/24468697f5df5feccdf3421f7a57c964e9e4c150 and it revealed, https://travis-ci.org/boostorg/gil/jobs/561828919, that the basic `sphinx-build --version` is failing:  
  
```  
Installing collected packages: alabaster, MarkupSafe, Jinja2, snowballstemmer, pyparsing, packaging, pytz, babel, imagesize, Pygments, typing, docutils, sphinxcontrib-websupport, sphinx  
Successfully installed Jinja2-2.10.1 MarkupSafe-1.1.1 Pygments-2.4.2 alabaster-0.7.12 babel-2.7.0 docutils-0.15 imagesize-1.1.0 packaging-19.0 pyparsing-2.4.1 pytz-2019.1 snowballstemmer-1.9.0 sphinx-1.7.9 sphinxcontrib-websupport-1.1.2 typing-3.7.4  
You are using pip version 19.0.3, however version 19.1.1 is available.  
You should consider upgrading via the 'pip install --upgrade pip' command.  
Traceback (most recent call last):  
  File "/home/travis/.local/bin/sphinx-build", line 10, in <module>  
    sys.exit(main())  
  File "/home/travis/.local/lib/python2.7/site-packages/sphinx/cmd/build.py", line 38, in main  
    return build_main(argv)  
  File "/home/travis/.local/lib/python2.7/site-packages/sphinx/cmd/build.py", line 22, in build_main  
    from sphinx import cmdline  
  File "/home/travis/.local/lib/python2.7/site-packages/sphinx/cmdline.py", line 19, in <module>  
    from docutils.utils import SystemMessage  
  File "/home/travis/.local/lib/python2.7/site-packages/docutils/utils/__init__.py", line 21, in <module>  
    import docutils.io  
  File "/home/travis/.local/lib/python2.7/site-packages/docutils/io.py", line 348  
    (self.destination.mode, mode)), file=self._stderr)  
                                        ^  
SyntaxError: invalid syntax  
```  
  
-----  
  
I have just run similar test on Ubuntu Bionic, installing the very same Sphinx 1.7.9 on Python 2.7:  
  
![image](https://user-images.githubusercontent.com/80741/61598171-e228df00-ac19-11e9-9277-48a657291236.png)  
  
Has anything changed in Python 2.7 or Sphinx 1.7.9 environment on Ubuntu?  
  
/cc @simmplecoder, @stefanseefeld

---

## Comment 1

> Username: mloskot  
> Created at: 2019-07-21 22:54:49 UTC  
> Url: https://github.com/boostorg/gil/issues/345#issuecomment-513594665  

Update to Sphinx 1.8.5 (still on Python 2.7) did not help  
  
![image](https://user-images.githubusercontent.com/80741/61598266-44361400-ac1b-11e9-806e-2008470b1967.png)
