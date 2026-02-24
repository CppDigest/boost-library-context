# #56 Update python to match jam. Various other fixes. [Closed]

> Username: frenchtoast747  
> Created at: 2015-01-31 18:12:38 UTC  
> Updated at: 2021-10-02 22:20:18 UTC  
> Closed at: 2015-09-16 17:50:58 UTC  
> Url: https://github.com/boostorg/build/pull/56  

The majority of the changes in this PR are to bring the Python port up to date with the Jam counterparts. Other various bug fixes are included.  
### feature.py  
- Added `__hash__` to the `Feature` class. `Feature` instances were being used in sets/dicts. When testing if a set/dict contained a specific feature, it would always return `False` since by default an object's hash is its `id`.  
- the call to `__select_subfeatures` was missing  
- the rest of the changes are just various Pythonic changes and aren't really necessary  
### generators.py  
- at some point the variable and return value `bypassed` was removed from the Jam code and was not updated in the Python port.  
- `convert_multiple_sources_to_consumable_types` was completely different on the Jam side. The Python port has just been updated to reflect the changes.  
- There was a slight error in translation from Jam to Python in `consume_directly`  
### project.py  
- unless I've done something wrong, I wasn't able to call `project.initialize` in the same way that I could in Jam code. I've added the `standalone_path` parameter so that the `source-location` parameter could be properly set.  
- with the above change required another change in `load_module`. The `loaded_tool_module_path_[mname]` needed to be set _before_ the module was imported.  
### property.py  
- allow `set_abbreviated_paths` to be called from Jam. Since the default value enables abbreviated paths and it's impossible for Jam to pass in a falsy value, the function now checks to see if the string `off` has been passed and disables appropriately.  
- The rest of the changes in `refine` have been added due to a bug that I noticed. I'm not sure if this is the correct place to add it or not. There were certain instances where a property set would have the properties `<toolset>arm` and `<toolset-arm:version>5.4`. Later on, in a call to `refine` when the `requirements` parameter would contain `<toolset>msvc`, the property `<toolset>arm` was being replaced, however, `<toolset-arm:version>5.4` was not. This cause problems later on in a call to `__select_alternatives` in `targets.py` when the best alternative was not being correctly picked due to that incorrect property.  
### targets.py  
- according to the comments and the code in the corresponding Jam file, a property is free and unconditional if it is also not incidental.  
- the rest of the changes are made to bring the port up to date with the Jam  
### toolset.py  
- `ungristed` is undefined; using the correct variable `value` instead  
### type.py  
- allow `register` to register an inherited type before the base type is registered  
- correct a parameter name in `set_generated_target_suffix`. This was found after implementing the `bjam_signature` type checking in #55.  
### builtin.py  
- In a previous pull request, I had ported the `CScanner` in the `types/cpp.py` module. I realized later that the `CScanner` had already been ported in the `builtin` module.  
- Updated the `LibGenerator` to match the Jam counterpart.  
### msvc.py  
- fixed an action naming error  
- `feature.get_values` returns a list and `compiler` is expected to be a string in the calls to `toolset.flags` later on.  
- `feature.subfeature` expects the subfeature value to be a list not a string. This was also found after the type checking from #55.  
### cpp.py  
- remove the `CScanner` since it has already been ported in the `builtin` module.

---

## Comment 1

> Username: frenchtoast747  
> Created_at: 2015-09-16 17:50:58 UTC  
> Url: https://github.com/boostorg/build/pull/56#issuecomment-140818682  

I'm going to close this and create separate pull requests for each of the issues for manageability purposes.

---
