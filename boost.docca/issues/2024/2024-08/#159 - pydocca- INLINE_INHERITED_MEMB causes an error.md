# #159 - pydocca: INLINE_INHERITED_MEMB causes an error [Closed]

> Username: anarthal  
> Created at: 2024-08-14 21:03:01 UTC  
> Updated at: 2024-10-31 11:25:52 UTC  
> Closed at: 2024-10-31 11:25:52 UTC  
> Url: https://github.com/boostorg/docca/issues/159  

When processing code using any form of inheritance, like:  
  
```  
/// A base class.  
struct base  
{  
    /// A function.  
    void f() {}  
};  
  
/// A derived class.  
struct derived : base  
{  
};  
```  
  
And setting the Doxygen parameter as per `<doxygen:param>INLINE_INHERITED_MEMB=YES`, the following error happens:  
  
```  
Traceback (most recent call last):  
  File "/home/ruben/workspace/boost-root/tools/docca/docca.py", line 1284, in <module>  
    main(sys.argv, sys.stdin, sys.stdout, os.path.realpath(__file__))  
  File "/home/ruben/workspace/boost-root/tools/docca/docca.py", line 1281, in main  
    render(env, template, file, data)  
  File "/home/ruben/workspace/boost-root/tools/docca/docca.py", line 1259, in render  
    template.stream(entities=data).dump(output)  
  File "/home/ruben/anaconda3/lib/python3.8/site-packages/jinja2/environment.py", line 1626, in dump  
    real_fp.writelines(iterable)  
  File "/home/ruben/anaconda3/lib/python3.8/site-packages/jinja2/environment.py", line 1670, in __next__  
    return self._next()  # type: ignore  
  File "/home/ruben/anaconda3/lib/python3.8/site-packages/jinja2/environment.py", line 1357, in generate  
    yield self.environment.handle_exception()  
  File "/home/ruben/anaconda3/lib/python3.8/site-packages/jinja2/environment.py", line 939, in handle_exception  
    raise rewrite_traceback_stack(source=source)  
  File "/home/ruben/workspace/boost-root/tools/docca/include/docca/quickbook.jinja2", line 318, in top-level template code  
    {{ comps.write_namespace(entity) }}  
  File "/home/ruben/anaconda3/lib/python3.8/site-packages/jinja2/runtime.py", line 782, in _invoke  
    rv = self._func(*arguments)  
  File "/home/ruben/workspace/boost-root/tools/docca/include/docca/quickbook/components.jinja2", line 29, in template  
    {{ write_type(m) }}  
  File "/home/ruben/anaconda3/lib/python3.8/site-packages/jinja2/runtime.py", line 782, in _invoke  
    rv = self._func(*arguments)  
  File "/home/ruben/workspace/boost-root/tools/docca/include/docca/quickbook/components.jinja2", line 43, in template  
    {% call(segment) section(entity) %}  
  File "/home/ruben/anaconda3/lib/python3.8/site-packages/jinja2/runtime.py", line 782, in _invoke  
    rv = self._func(*arguments)  
  File "/home/ruben/workspace/boost-root/tools/docca/include/docca/quickbook/components.jinja2", line 364, in template  
    {{ caller("summary") }}  
  File "/home/ruben/anaconda3/lib/python3.8/site-packages/jinja2/runtime.py", line 782, in _invoke  
    rv = self._func(*arguments)  
  File "/home/ruben/workspace/boost-root/tools/docca/include/docca/quickbook/components.jinja2", line 53, in template  
    {{ function_summary_table(  
  File "/home/ruben/anaconda3/lib/python3.8/site-packages/jinja2/runtime.py", line 782, in _invoke  
    rv = self._func(*arguments)  
  File "/home/ruben/workspace/boost-root/tools/docca/include/docca/quickbook/components.jinja2", line 491, in template  
    {%- call(member) summary_table(  
  File "/home/ruben/anaconda3/lib/python3.8/site-packages/jinja2/runtime.py", line 782, in _invoke  
    rv = self._func(*arguments)  
  File "/home/ruben/workspace/boost-root/tools/docca/include/docca/quickbook/components.jinja2", line 528, in template  
    {{ caller(row) }}  
  File "/home/ruben/anaconda3/lib/python3.8/site-packages/jinja2/runtime.py", line 782, in _invoke  
    rv = self._func(*arguments)  
  File "/home/ruben/workspace/boost-root/tools/docca/include/docca/quickbook/components.jinja2", line 499, in template  
    {%- for part in member.brief -%}  
  File "/home/ruben/anaconda3/lib/python3.8/site-packages/jinja2/runtime.py", line 422, in __init__  
    self._iterator = self._to_iterator(iterable)  
  File "/home/ruben/anaconda3/lib/python3.8/site-packages/jinja2/runtime.py", line 430, in _to_iterator  
    return iter(iterable)  
jinja2.exceptions.UndefinedError: '__main__.OverloadSet object' has no attribute 'brief'  
```  
  
The entities dictionary in `docca.py` seems to contain a repeated entry for `base::f`, and only one of these is getting resolved. The error is actually misleading, as it's the error is caused by one of the `Function` objects, not having the `brief` attribute (because resolve was never called on it).  
  
To be fair, what I'd want as a user (and will try to implement in my asciidoc version) is a way to show inherited members in the derived class page, more than duplicating member entries. So a change in the Jinja template may be enough.
