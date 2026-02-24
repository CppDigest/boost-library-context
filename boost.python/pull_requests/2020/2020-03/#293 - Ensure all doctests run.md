# #293 Ensure all doctests run [Merged]

> Username: tkieft  
> Created at: 2020-03-19 20:30:04 UTC  
> Updated at: 2020-08-08 06:19:04 UTC  
> Merged at: 2020-08-08 06:19:04 UTC  
> Closed at: 2020-08-08 06:19:04 UTC  
> Url: https://github.com/boostorg/python/pull/293  

905b34210b9ae2310c61b52584313e33b7f131ca added Python 3 compatibility for the tests. However, due to the behavior of doctest, this silently disabled some of the tests. For doctest to pick up module docstrings, the docstring has to be the first statement of the module. You can see that the tests are failing to execute if you add `verbose=True` to the `doctest.testmod()` call.  
  
This PR fixes this error for all 12 modules where there was a non-docstring as the first statement, and then adds minor fixups to the tests to make sure they execute on both Python 2 and Python 3.

---

## Review 1 [Commented]

> Username: stefanseefeld  
> Created_at: 2020-03-19 20:44:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/python/pull/293#pullrequestreview-378074382  

Many thanks for working on this !

📁 test/dict.cpp

```diff
  28 |+ 
  29 |+     tmp1["key1"] = "value1";
  30 |+ 
```

> Username: stefanseefeld  
> Created_at: 2020-03-19 20:36:16 UTC  
> Updated_at: 2020-03-23 16:12:48 UTC  
> Url: https://github.com/boostorg/python/pull/293#discussion_r395304752  

Just for avoidance of doubt: this reflects the fact that in current versions of Python the order reported (notably when printing a dict) corresponds to the insertion order, yes ?

> Username: tkieft  
> Created_at: 2020-03-19 20:48:47 UTC  
> Updated_at: 2020-03-23 16:12:48 UTC  
> Url: https://github.com/boostorg/python/pull/293#discussion_r395311071  

Yes! If we want to print out the full dictionary, we have to make sure the output will be the same across Python versions. I'm not sure what ordering Python 2 is using (by hash, perhaps), but Python 3 is using insertion order.

> Username: stefanseefeld  
> Created_at: 2020-03-19 20:51:25 UTC  
> Updated_at: 2020-03-23 16:12:48 UTC  
> Url: https://github.com/boostorg/python/pull/293#discussion_r395312323  

Right. So if ever we find the order to vary (for older Python versions, where the order isn't mandated yet), we need to rethink how to print the dict...

> Username: tkieft  
> Created_at: 2020-03-19 20:58:59 UTC  
> Updated_at: 2020-03-23 16:12:48 UTC  
> Url: https://github.com/boostorg/python/pull/293#discussion_r395316105  

I think I tested each case on my local copy of Python2, and the order seemed stable. Hopefully the same shows up in the CI.


---

## Comment 2

> Username: tkieft  
> Created_at: 2020-03-20 14:21:41 UTC  
> Url: https://github.com/boostorg/python/pull/293#issuecomment-601725698  

Looking at the test failures, let me update :)

---

## Comment 3

> Username: tkieft  
> Created_at: 2020-03-20 14:29:12 UTC  
> Url: https://github.com/boostorg/python/pull/293#issuecomment-601729642  

I think one of the errors (`test.map_indexing_suite`) is due to faber not building the native extension correctly. Let me try submitting a separate PR to update Travis to use faber-0.3 (which works on my machine) and see if that will fix things.

---

## Review 4 [Commented]

> Username: tkieft  
> Created_at: 2020-03-20 17:58:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/python/pull/293#pullrequestreview-378700513  

📁 test/test_builtin_converters.py

```diff
 135 | yo, wassup?
 136 | 
 139 |- >>> print(rewrap_value_wstring(u'\U0001f4a9'))
```

> Username: tkieft  
> Created_at: 2020-03-20 17:58:33 UTC  
> Updated_at: 2020-03-23 16:12:48 UTC  
> Url: https://github.com/boostorg/python/pull/293#discussion_r395803944  

I had to remove this test; there was no way to get it to work on Python 2 & 3 simultaneously. Luckily the `rewrap_value_wstring` function is still tested above.


---

## Comment 5

> Username: tkieft  
> Created_at: 2020-03-20 18:00:10 UTC  
> Url: https://github.com/boostorg/python/pull/293#issuecomment-601837344  

@stefanseefeld I think I've fixed all the test errors except for `map_indexing_suite`. The problem there is that faber is building the extension with the wrong name (`a_map_indexing_suite` instead of `map_indexing_suite_ext`).  This doesn't happen on my machine, and I can't figure out why it's happening. Can you help debug?

---

## Comment 6

> Username: stefanseefeld  
> Created_at: 2020-03-23 16:27:02 UTC  
> Url: https://github.com/boostorg/python/pull/293#issuecomment-602709423  

Yes, I'll look into the faber issue. (I don't have a Windows machine, and running things on a Windows VM is a bit painful.)

---

## Comment 7

> Username: tkieft  
> Created_at: 2020-03-23 16:34:44 UTC  
> Url: https://github.com/boostorg/python/pull/293#issuecomment-602714229  

Actually, hold off for a moment - I think I might have fixed it. Just pushed a new build to check 20 minutes ago :)

---

## Review 8 [Commented]

> Username: tkieft  
> Created_at: 2020-03-23 16:35:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/python/pull/293#pullrequestreview-379607273  

📁 test/fabscript

```diff
  44 |         else:  # build from a list of source files
  45 |-             n = e[0] if e[0] != name else e[0] + '_ext'
  46 |-             s = [n + '.cpp' for n in e]
```

> Username: tkieft  
> Created_at: 2020-03-23 16:35:41 UTC  
> Updated_at: 2020-03-23 16:35:54 UTC  
> Url: https://github.com/boostorg/python/pull/293#discussion_r396589648  

In Python 2, this `n` will leak out of the generator and trample over the previous value.

> Username: stefanseefeld  
> Created_at: 2020-03-23 16:38:54 UTC  
> Url: https://github.com/boostorg/python/pull/293#discussion_r396591931  

Great finding ! Good that Python 2 has reached its end of life. I'm about to remove Python 2 support from Faber, too...


---

## Comment 9

> Username: tkieft  
> Created_at: 2020-03-23 19:20:25 UTC  
> Updated_at: 2020-03-23 19:38:39 UTC  
> Url: https://github.com/boostorg/python/pull/293#issuecomment-602805016  

OK, Travis succeeded, I think this is ready for a closer look! The AppVeyor failures it looks like you're already aware of, and are unrelated to this diff (although it would be nice to have them working obviously!)

---

## Comment 10

> Username: tkieft  
> Created_at: 2020-04-03 12:39:46 UTC  
> Url: https://github.com/boostorg/python/pull/293#issuecomment-608410726  

@stefanseefeld Thoughts on merging this? Are you trying to find time to get the Windows CI running?

---

## Comment 11

> Username: tkieft  
> Created_at: 2020-04-28 21:30:11 UTC  
> Url: https://github.com/boostorg/python/pull/293#issuecomment-620866024  

@stefanseefeld - would really love to get this in!

---
