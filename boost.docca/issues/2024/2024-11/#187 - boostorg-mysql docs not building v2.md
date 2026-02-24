# #187 - boostorg/mysql docs not building v2 [Closed]

> Username: sdarwin  
> Created at: 2024-11-01 11:08:01 UTC  
> Updated at: 2024-11-01 14:50:37 UTC  
> Closed at: 2024-11-01 14:50:36 UTC  
> Url: https://github.com/boostorg/docca/issues/187  

An error: https://github.com/sdarwin/release-tools/actions/runs/11620561117/job/32362609715  
  
```  
  File "/Users/runner/work/release-tools/release-tools/github/boostorg/boost/tools/docca/include/docca/quickbook.jinja2", line 323, in top-level template code  
    {{ comps.write_namespace(entity) }}  
    ^^^^^^^^^^^^^^^^^^^^^  
  File "/Users/runner/venvboostdocs/lib/python3.13/site-packages/jinja2/runtime.py", line 777, in _invoke  
    rv = self._func(*arguments)  
  File "/Users/runner/work/release-tools/release-tools/github/boostorg/boost/tools/docca/include/docca/quickbook/components.jinja2", line 32, in template  
    {%- for m in entity.members.values() | select("OverloadSet") | sort -%}  
    ^^^^^^^^^^^^^^^^^^^^^  
  File "/Users/runner/venvboostdocs/lib/python3.13/site-packages/jinja2/filters.py", line 409, in do_sort  
    return sorted(value, key=key_func, reverse=reverse)  
  File "/Users/runner/work/release-tools/release-tools/github/boostorg/boost/tools/docca/docca.py", line 1014, in __lt__  
    return self.funcs[0] < other.funcs[0]  
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^  
  File "/Users/runner/work/release-tools/release-tools/github/boostorg/boost/tools/docca/docca.py", line 936, in __lt__  
    if self.scope == parent.scope:  
                     ^^^^^^  
NameError: name 'parent' is not defined. Did you mean: 'print'?  
```

---

## Comment 1

> Username: grisumbras  
> Created at: 2024-11-01 11:48:38 UTC  
> Url: https://github.com/boostorg/docca/issues/187#issuecomment-2451748176  

This error should be fixed in develop via #186.

---

## Comment 2

> Username: sdarwin  
> Created at: 2024-11-01 14:50:36 UTC  
> Url: https://github.com/boostorg/docca/issues/187#issuecomment-2452004341  

yes I think it's fixed.
