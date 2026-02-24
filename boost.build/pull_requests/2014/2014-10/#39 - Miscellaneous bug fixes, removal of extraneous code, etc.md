# #39 Miscellaneous bug fixes, removal of extraneous code, etc. [Merged]

> Username: frenchtoast747  
> Created at: 2014-10-06 18:32:20 UTC  
> Updated at: 2021-10-02 22:35:38 UTC  
> Merged at: 2014-10-07 07:26:02 UTC  
> Closed at: 2014-10-07 07:26:02 UTC  
> Url: https://github.com/boostorg/build/pull/39  

#### feature.py:  
  
Use the feature's name in the error output. This helps in understanding the error message.  
#### project.py:  
  
The original looked like the result of a bad merge as the return value was the exact same as the return within the `try...except` block. The `try...except` produces a better error message upon failure.  
#### property_set.py:  
  
The `string` module is unused.  
#### virtual_target.py:  
  
ARM's `ar.exe` would fail when passed a path to the `-via` file that had both POSIX and Windows path separators. Normalizing the path on the target solved the problem.  
#### common.py:  
  
The `requirement` parameter in `check_init_parameters` should be a container. The original Jam signature marks it as a list with zero or more items. These changes convert the requirement parameter to using only a container.  
#### msvc.py:  
  
The call to `to_seq` asserts that command is always some container. So, `extend()` should always be used.  
#### tools/rc.py:  
  
Old style exceptions aren't allowed (at least in Python 2.7). This change just uses the existing `AlreadyDefined` exception.  
#### testing.py:  
  
`loaded_tool_module_path_`  is empty upon initial import. Using the `__file__` variable instead will always work (provided the import system doesn't change).

---
