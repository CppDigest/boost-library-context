# #224 Convert to code fences [Merged]

> Username: pfultz2  
> Created at: 2023-01-11 20:40:54 UTC  
> Updated at: 2023-01-23 19:06:56 UTC  
> Merged at: 2023-01-12 00:32:42 UTC  
> Closed at: 2023-01-12 00:32:42 UTC  
> Url: https://github.com/boostorg/hof/pull/224  



---

## Comment 1

> Username: sdarwin  
> Created_at: 2023-01-11 21:30:43 UTC  
> Url: https://github.com/boostorg/hof/pull/224#issuecomment-1379509301  

The thing is to test with the new Sphinx.    Here's a Dockerfile with all the new packages for building boost docs:   https://github.com/boostorg/release-tools/pull/34   That's an option: to run inside a docker container with just those packages, and see what the result is.  Or, if you reach a stopping point and believe it's ready to test, let me know.

---

## Comment 2

> Username: pfultz2  
> Created_at: 2023-01-11 23:25:35 UTC  
> Url: https://github.com/boostorg/hof/pull/224#issuecomment-1379610120  

I tested this with the versions in doc/requirements.txt. It seems to look correct, if you want to test it out and let me know.

---

## Comment 3

> Username: sdarwin  
> Created_at: 2023-01-12 00:21:37 UTC  
> Url: https://github.com/boostorg/hof/pull/224#issuecomment-1379650378  

Wow, that was like magic!!  Great job.     
I reviewed the results with the new sphinx and it appears to be correct.  At least, didn't notice a problem.    
  
After this pull request is done, the final holdout is boostorg/python. I sent them a similar sphinx update, but not about code fences.   Then, the larger boost upgrade can go forward.

---

## Comment 4

> Username: sdarwin  
> Created_at: 2023-01-23 19:06:55 UTC  
> Url: https://github.com/boostorg/hof/pull/224#issuecomment-1400837708  

@pfultz2 could all the sphinx doc updates be merged into the master branch?    
  
Testing the release-tools upgrade, if it gets applied to the `master` branch of boost these errors appear:    
  
```  
Jamfile</root/project/libs/hof/doc>.make-hof-doc /root/build/boost/bin.v2/libs/hof/doc/hof-doc  
Running Sphinx v5.2.1  
  
Configuration error:  
There is a programmable error in your configuration file:  
  
Traceback (most recent call last):  
  File "/root/.local/lib/python3.8/site-packages/sphinx/config.py", line 349, in eval_config_file  
    exec(code, namespace)  
  File "/root/project/libs/hof/doc/conf.py", line 26, in <module>  
    from recommonmark.parser import CommonMarkParser  
ModuleNotFoundError: No module named 'recommonmark'  
  
  
    sphinx-build -b html /root/project/libs/hof/doc /root/project/libs/hof/doc/html/  
  
...failed Jamfile</root/project/libs/hof/doc>.make-hof-doc /root/build/boost/bin.v2/libs/hof/doc/hof-doc...  
```

---
