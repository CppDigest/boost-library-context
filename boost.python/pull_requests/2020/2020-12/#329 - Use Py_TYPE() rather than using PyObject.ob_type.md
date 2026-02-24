# #329 Use Py_TYPE() rather than using PyObject.ob_type [Closed]

> Username: vstinner  
> Created at: 2020-12-09 00:57:19 UTC  
> Updated at: 2022-11-09 16:17:21 UTC  
> Closed at: 2022-11-09 16:17:20 UTC  
> Url: https://github.com/boostorg/python/pull/329  

Py_TYPE() is available since Python 2.6.

---

## Comment 1

> Username: vstinner  
> Created_at: 2020-12-09 00:58:06 UTC  
> Url: https://github.com/boostorg/python/pull/329#issuecomment-741354688  

PR created by the "Py_TYPE" operation of the upgrade_pythoncapi.py script of https://github.com/pythoncapi/pythoncapi_compat

---

## Comment 2

> Username: vstinner  
> Created_at: 2020-12-09 01:07:12 UTC  
> Updated_at: 2020-12-09 01:08:12 UTC  
> Url: https://github.com/boostorg/python/pull/329#issuecomment-741367407  

See also https://github.com/boostorg/python/pull/330

---

## Comment 3

> Username: vstinner  
> Created_at: 2020-12-16 11:51:34 UTC  
> Url: https://github.com/boostorg/python/pull/329#issuecomment-746169241  

Ah, the change was wrong on tests: x.get() should be used to get the `PyObject*`.

---

## Comment 4

> Username: vstinner  
> Created_at: 2022-11-09 16:17:20 UTC  
> Url: https://github.com/boostorg/python/pull/329#issuecomment-1309005168  

No feedback in 2 years. I just close my PR.

---
