# #1014 - Docca docs build [Closed]

> Username: sdarwin  
> Created at: 2024-06-18 15:16:50 UTC  
> Updated at: 2024-06-21 19:41:53 UTC  
> Closed at: 2024-06-21 19:41:53 UTC  
> Url: https://github.com/boostorg/json/issues/1014  

In https://github.com/boostorg/release-tools, build_docs scripts generate boost library docs on linux, windows, and macos platforms. The CI tests run on all those platforms, testing the build process.    
  
Recently boostorg/json is failing with docca errors on macos and windows.  The cause of the problem could be the scripts themselves.   Or a docca issue.   
  
Any ideas?  
  
https://github.com/boostorg/release-tools/actions/runs/9566036415/job/26370337036  
  
`AssertionError`  
  
```  
docca.touch-action bin.v2/libs/json/doc/_reference-dir/index.xml  
docca.generate-reference bin.v2/libs/json/doc/reference.qbk  
Traceback (most recent call last):  
  File "/Users/runner/work/release-tools/release-tools/github/boostorg/boost/tools/docca/docca.py", line 1282, in <module>  
    main(sys.argv, sys.stdin, sys.stdout, os.path.realpath(__file__))  
  File "/Users/runner/work/release-tools/release-tools/github/boostorg/boost/tools/docca/docca.py", line 1265, in main  
    data = collect_data(data_dir, refs)  
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^  
  File "/Users/runner/work/release-tools/release-tools/github/boostorg/boost/tools/docca/docca.py", line 1175, in collect_data  
    factory(element, result)  
  File "/Users/runner/work/release-tools/release-tools/github/boostorg/boost/tools/docca/docca.py", line 767, in __init__  
    super().__init__(element, None, index)  
  File "/Users/runner/work/release-tools/release-tools/github/boostorg/boost/tools/docca/docca.py", line 740, in __init__  
    member = factory(member_def, section, self, index)  
             ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^  
  File "/Users/runner/work/release-tools/release-tools/github/boostorg/boost/tools/docca/docca.py", line 805, in __init__  
    super().__init__(element, parent, index)  
  File "/Users/runner/work/release-tools/release-tools/github/boostorg/boost/tools/docca/docca.py", line 689, in __init__  
    super().__init__(element, scope, index)  
  File "/Users/runner/work/release-tools/release-tools/github/boostorg/boost/tools/docca/docca.py", line 668, in __init__  
    super().__init__(element, scope, index)  
  File "/Users/runner/work/release-tools/release-tools/github/boostorg/boost/tools/docca/docca.py", line 652, in __init__  
    super().__init__(element, scope, index)  
  File "/Users/runner/work/release-tools/release-tools/github/boostorg/boost/tools/docca/docca.py", line 537, in __init__  
    assert self.name  
           ^^^^^^^^^  
AssertionError  
  
```

---

## Comment 1

> Username: grisumbras  
> Created at: 2024-06-18 15:52:56 UTC  
> Url: https://github.com/boostorg/json/issues/1014#issuecomment-2176438682  

Just checked with Doxygen 1.11, which is installed by release-tools on MacOS. And the error manifests locally. So, something has changed in Doxygen output. I'll try to fix this as soon as possible.

---

## Comment 2

> Username: grisumbras  
> Created at: 2024-06-18 16:07:19 UTC  
> Url: https://github.com/boostorg/json/issues/1014#issuecomment-2176474711  

boostorg/docca#150 should fix the issue

---

## Comment 3

> Username: grisumbras  
> Created at: 2024-06-19 06:01:44 UTC  
> Url: https://github.com/boostorg/json/issues/1014#issuecomment-2177816082  

Should be fixed now

---

## Comment 4

> Username: sdarwin  
> Created at: 2024-06-19 16:23:01 UTC  
> Url: https://github.com/boostorg/json/issues/1014#issuecomment-2179081076  

New docca errors on Windows 2019 and Windows 2022.  
  
https://github.com/boostorg/release-tools/actions/runs/9567848900/job/26424016691    
https://github.com/boostorg/release-tools/actions/runs/9567848900/job/26424016327  
  
`jinja2.exceptions.UndefinedError: '__main__.Phrase object' has no attribute 'unhandled_type'`  
  
```  
  
    "python" "C:\boostorg\boost\tools\docca\docca.py" -i"bin.v2\libs\json\doc\_reference-dir\index.xml" -o"bin.v2\libs\json\doc\reference.qbk" -c"libs\json\doc\config.json" -c"bin.v2\libs\json\doc\_reference-dir\docca-config.json" -T"C:\boostorg\boost\tools\docca\include\docca\quickbook.jinja2" -I"."  
  
Traceback (most recent call last):  
  File "C:\boostorg\boost\tools\docca\docca.py", line 1281, in <module>  
    main(sys.argv, sys.stdin, sys.stdout, os.path.realpath(__file__))  
  File "C:\boostorg\boost\tools\docca\docca.py", line 1278, in main  
    render(env, template, file, data)  
  File "C:\boostorg\boost\tools\docca\docca.py", line 1256, in render  
    template.stream(entities=data).dump(output)  
  File "C:\hostedtoolcache\windows\Python\3.9.13\x64\lib\site-packages\jinja2\environment.py", line 1618, in dump  
    fp.writelines(iterable)  
  File "C:\hostedtoolcache\windows\Python\3.9.13\x64\lib\site-packages\jinja2\environment.py", line 1662, in __next__  
    return self._next()  # type: ignore  
  File "C:\hostedtoolcache\windows\Python\3.9.13\x64\lib\site-packages\jinja2\environment.py", line 1354, in generate  
    yield self.environment.handle_exception()  
  File "C:\hostedtoolcache\windows\Python\3.9.13\x64\lib\site-packages\jinja2\environment.py", line 936, in handle_exception  
    raise rewrite_traceback_stack(source=source)  
  File "C:\boostorg\boost\tools\docca\include\docca\quickbook.jinja2", line 318, in top-level template code  
    {{ comps.write_namespace(entity) }}  
  File "C:\hostedtoolcache\windows\Python\3.9.13\x64\lib\site-packages\jinja2\runtime.py", line 777, in _invoke  
    rv = self._func(*arguments)  
  File "C:\boostorg\boost\tools\docca\include\docca\quickbook\components.jinja2", line 29, in template  
    {{ write_type(m) }}  
  File "C:\hostedtoolcache\windows\Python\3.9.13\x64\lib\site-packages\jinja2\runtime.py", line 777, in _invoke  
    rv = self._func(*arguments)  
  File "C:\boostorg\boost\tools\docca\include\docca\quickbook\components.jinja2", line 43, in template  
    {% call(segment) section(entity) %}  
  File "C:\hostedtoolcache\windows\Python\3.9.13\x64\lib\site-packages\jinja2\runtime.py", line 777, in _invoke  
    rv = self._func(*arguments)  
  File "C:\boostorg\boost\tools\docca\include\docca\quickbook\components.jinja2", line 379, in template  
    {{ caller("members") }}  
  File "C:\hostedtoolcache\windows\Python\3.9.13\x64\lib\site-packages\jinja2\runtime.py", line 777, in _invoke  
    rv = self._func(*arguments)  
  File "C:\boostorg\boost\tools\docca\include\docca\quickbook\components.jinja2", line 180, in template  
    {{ write_entity(member) }}  
  File "C:\hostedtoolcache\windows\Python\3.9.13\x64\lib\site-packages\jinja2\runtime.py", line 777, in _invoke  
    rv = self._func(*arguments)  
  File "C:\boostorg\boost\tools\docca\include\docca\quickbook\components.jinja2", line 17, in template  
    {{ write_overload_set(entity) }}  
  File "C:\hostedtoolcache\windows\Python\3.9.13\x64\lib\site-packages\jinja2\runtime.py", line 777, in _invoke  
    rv = self._func(*arguments)  
  File "C:\boostorg\boost\tools\docca\include\docca\quickbook\components.jinja2", line 310, in template  
    {{ function_declaration(func, linked=True) }}  
  File "C:\hostedtoolcache\windows\Python\3.9.13\x64\lib\site-packages\jinja2\runtime.py", line 777, in _invoke  
    rv = self._func(*arguments)  
  File "C:\boostorg\boost\tools\docca\include\docca\quickbook\components.jinja2", line 385, in template  
    {{ template_parameters(entity) }}  
  File "C:\hostedtoolcache\windows\Python\3.9.13\x64\lib\site-packages\jinja2\runtime.py", line 777, in _invoke  
    rv = self._func(*arguments)  
  File "C:\boostorg\boost\tools\docca\include\docca\quickbook\components.jinja2", line 474, in template  
    {%- if tparam.default_value %} = {{ phrase(tparam.default_value, in_code=True) }}{% endif -%}  
  File "C:\hostedtoolcache\windows\Python\3.9.13\x64\lib\site-packages\jinja2\runtime.py", line 777, in _invoke  
    rv = self._func(*arguments)  
  File "C:\boostorg\boost\tools\docca\include\docca\quickbook\components.jinja2", line 661, in template  
    {%- for part in para -%}{{ phrase_part(part, in_code=in_code) }}{%- endfor -%}  
  File "C:\hostedtoolcache\windows\Python\3.9.13\x64\lib\site-packages\jinja2\runtime.py", line 777, in _invoke  
    rv = self._func(*arguments)  
  File "C:\boostorg\boost\tools\docca\include\docca\quickbook\components.jinja2", line 696, in template  
    {{ part.unhandled_type() }}  
  File "C:\hostedtoolcache\windows\Python\3.9.13\x64\lib\site-packages\jinja2\utils.py", line 83, in from_obj  
    if hasattr(obj, "jinja_pass_arg"):  
jinja2.exceptions.UndefinedError: '__main__.Phrase object' has no attribute 'unhandled_type'  
...failed docca.generate-reference bin.v2\libs\json\doc\reference.qbk...  
```

---

## Comment 5

> Username: grisumbras  
> Created at: 2024-06-21 06:17:52 UTC  
> Url: https://github.com/boostorg/json/issues/1014#issuecomment-2182078954  

The fix is in boostorg/docca#151. Please try now.

---

## Comment 6

> Username: sdarwin  
> Created at: 2024-06-21 19:41:53 UTC  
> Url: https://github.com/boostorg/json/issues/1014#issuecomment-2183350788  

Windows docs succeeded.
