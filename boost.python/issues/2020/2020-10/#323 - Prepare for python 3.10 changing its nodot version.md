# #323 - Prepare for python 3.10 changing its nodot version [Open]

> Username: mattip  
> Created at: 2020-10-15 19:31:59 UTC  
> Updated at: 2020-10-15 19:53:13 UTC  
> Url: https://github.com/boostorg/python/issues/323  

As long as I am lurking in your repo bothering you about PyPy, I would like to mention that python [may change the "nodot" convention](https://github.com/python/cpython/pull/20333) for `3.10` to become `3_10` rather than `310`. This might affect code like [here](https://github.com/boostorg/python/blob/15115eb1f844e9ac8ed5dd454d46a4e7b5ae5b48/fabscript#L23) (probably a bad example) that concatenates the python major-minor versions. A better technique would be to avoid thinking about it and use `sysconfig.get_config_var('py_version_nodot')`.  
  
Feel free to close this with no comment if it is not relevant.

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2020-10-15 19:53:13 UTC  
> Url: https://github.com/boostorg/python/issues/323#issuecomment-709555681  

It certainly is relevant, thanks for reviewing the code !
