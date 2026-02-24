# #356 Check library test for macros without a BOOST_ prefix. [Closed]

> Username: ned14  
> Created at: 2019-12-17 14:48:58 UTC  
> Updated at: 2020-04-09 23:29:50 UTC  
> Closed at: 2020-04-09 23:29:50 UTC  
> Url: https://github.com/boostorg/boost/pull/356  



---

## Review 1 [Commented]

> Username: Flamefire  
> Created_at: 2019-12-18 13:36:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/boost/pull/356#pullrequestreview-333961132  

Just did a quick scan over the code and annotated couple minor issues. Not sure about the full CPPP in Python... Wouldn't a simple grep suffice? (or a few of them) I see the difficulty and potential for false negatives there but the most common constructs are `#define X` `#if(def)? X`  `#if X <op> Y` and those can be detected with some regexp over preprocessor lines

📁 status/boost_check_library.py

```diff
 116 |+                     continue
 117 |+                 for item in files:
 118 |+                     ext = os.path.splitext(item)
```

> Username: Flamefire  
> Created_at: 2019-12-18 13:18:19 UTC  
> Updated_at: 2019-12-18 15:59:30 UTC  
> Url: https://github.com/boostorg/boost/pull/356#discussion_r359337182  

As you just just `ext[1]` use  `os.path.splitext(item)[1]`. Then "ext" really is the extension.

> Username: ned14  
> Created_at: 2019-12-18 14:32:09 UTC  
> Updated_at: 2019-12-18 15:59:30 UTC  
> Url: https://github.com/boostorg/boost/pull/356#discussion_r359374885  

Will fix

---

📁 status/boost_check_library.py

```diff
 118 |+                     ext = os.path.splitext(item)
 119 |+                     if ext[1] == '.hpp' or ext[1] == '.ipp' or ext[1] == '.h':
 120 |+                         p = Preprocessor(self)
```

> Username: Flamefire  
> Created_at: 2019-12-18 13:20:03 UTC  
> Updated_at: 2019-12-18 15:59:30 UTC  
> Url: https://github.com/boostorg/boost/pull/356#discussion_r359338024  

Can you put this in a function like `getBadMacros(file)`? The nesting gets really deep

> Username: ned14  
> Created_at: 2019-12-18 14:40:16 UTC  
> Updated_at: 2019-12-18 15:59:30 UTC  
> Url: https://github.com/boostorg/boost/pull/356#discussion_r359379713  

I don't think the nesting deeper than other code in the same file.

---

📁 status/boost_check_library.py

```diff
 110 |+     def check_organization_macros(self):
 111 |+         libdir = os.path.join(self.library_dir,'include','boost',self.library_name)
 112 |+         if os.path.isdir(libdir):
```

> Username: Flamefire  
> Created_at: 2019-12-18 13:20:15 UTC  
> Updated_at: 2019-12-18 15:59:30 UTC  
> Url: https://github.com/boostorg/boost/pull/356#discussion_r359338128  

Why not just return? Reduces nesting

> Username: ned14  
> Created_at: 2019-12-18 14:33:07 UTC  
> Updated_at: 2019-12-18 15:59:30 UTC  
> Url: https://github.com/boostorg/boost/pull/356#discussion_r359375435  

None of the other checks early-out. I was matching the style of the original source code. Some people have strong feelings on early-out.

> Username: Flamefire  
> Created_at: 2019-12-18 14:43:44 UTC  
> Updated_at: 2019-12-18 15:59:30 UTC  
> Url: https://github.com/boostorg/boost/pull/356#discussion_r359381735  

Never understood the reasoning for opposing early out on checks like this, especially if the function returns nothing. It really hinders readability. And other functions do (kind of) early out: https://github.com/boostorg/boost/blob/e6a40e1d69b795654f02f940ebfe2543b8dc6ad9/status/boost_check_library.py#L294-L304  
  
Counterexample is https://github.com/boostorg/boost/blob/e6a40e1d69b795654f02f940ebfe2543b8dc6ad9/status/boost_check_library.py#L333 but I'm not sure if this was decided or just happens to be the authors preference.

---

📁 status/boost_check_library.py

```diff
 109 |     
 110 |+     def check_organization_macros(self):
 111 |+         libdir = os.path.join(self.library_dir,'include','boost',self.library_name)
```

> Username: Flamefire  
> Created_at: 2019-12-18 13:20:41 UTC  
> Updated_at: 2019-12-18 15:59:30 UTC  
> Url: https://github.com/boostorg/boost/pull/356#discussion_r359338371  

nit: space after comma (maybe autopep8 this?)

> Username: ned14  
> Created_at: 2019-12-18 14:36:40 UTC  
> Updated_at: 2019-12-18 15:59:30 UTC  
> Url: https://github.com/boostorg/boost/pull/356#discussion_r359377590  

All spaces after commas removed

> Username: Flamefire  
> Created_at: 2019-12-18 14:38:21 UTC  
> Updated_at: 2019-12-18 15:59:30 UTC  
> Url: https://github.com/boostorg/boost/pull/356#discussion_r359378531  

I was actually suggesting to add the spaces for better readability. Sorry for the confusion

---

📁 status/boost_check_library.py

```diff
 115 |+                 if os.path.sep + 'detail' + os.path.sep in root or root.endswith(os.path.sep + 'detail'):
 116 |+                     continue
 117 |+                 for item in files:
```

> Username: Flamefire  
> Created_at: 2019-12-18 13:21:28 UTC  
> Updated_at: 2019-12-18 15:59:30 UTC  
> Url: https://github.com/boostorg/boost/pull/356#discussion_r359338772  

why `item` and not `file` or `current_file`? I had to look this up when reading `os.path.join(root, item)`

> Username: ned14  
> Created_at: 2019-12-18 14:34:30 UTC  
> Updated_at: 2019-12-18 15:59:30 UTC  
> Url: https://github.com/boostorg/boost/pull/356#discussion_r359376242  

The natural thing to use here is `for file in files:`, but in python file is a builtin function. And `current_file` takes longer to type. But I'll fix it anyway.

---

📁 status/boost_check_library.py

```diff
 130 |+                                 badmacros[macro] = path
 131 |+                                 if p.macros[macro].source:
 132 |+                                     badmacros[macro] = "%s:%d" % (p.macros[macro].source, p.macros[macro].lineno)
```

> Username: Flamefire  
> Created_at: 2019-12-18 13:22:17 UTC  
> Updated_at: 2019-12-18 15:59:30 UTC  
> Url: https://github.com/boostorg/boost/pull/356#discussion_r359339170  

Maybe make this a list of all occurrences?

> Username: ned14  
> Created_at: 2019-12-18 14:39:56 UTC  
> Updated_at: 2019-12-18 15:59:30 UTC  
> Url: https://github.com/boostorg/boost/pull/356#discussion_r359379484  

We shall await if a longer list is needed

---

📁 status/boost_check_library.py

```diff
 131 |+                                 if p.macros[macro].source:
 132 |+                                     badmacros[macro] = "%s:%d" % (p.macros[macro].source, p.macros[macro].lineno)
 133 |+             if badmacros:
```

> Username: Flamefire  
> Created_at: 2019-12-18 13:22:40 UTC  
> Updated_at: 2019-12-18 15:59:30 UTC  
> Url: https://github.com/boostorg/boost/pull/356#discussion_r359339359  

Not required. looping over an empty dict is fine an a no-op

> Username: ned14  
> Created_at: 2019-12-18 14:37:50 UTC  
> Updated_at: 2019-12-18 15:59:30 UTC  
> Url: https://github.com/boostorg/boost/pull/356#discussion_r359378233  

Fixed


📁 status/pcpp/.gitignore

```diff
   1 |+ *.pyc
```

> Username: Flamefire  
> Created_at: 2019-12-18 13:23:09 UTC  
> Updated_at: 2019-12-18 15:59:30 UTC  
> Url: https://github.com/boostorg/boost/pull/356#discussion_r359339580  

You may want to add `*.pyo` and `__pycache__` too

> Username: ned14  
> Created_at: 2019-12-18 14:39:39 UTC  
> Updated_at: 2019-12-18 15:59:30 UTC  
> Url: https://github.com/boostorg/boost/pull/356#discussion_r359379322  

Done


📁 status/pcpp/evaluator.py

```diff
  95 |+         return "Int(%d)" % INTBASETYPE(self)
  96 |+ 
  97 |+ #def execute_expr(token):
```

> Username: Flamefire  
> Created_at: 2019-12-18 13:24:47 UTC  
> Updated_at: 2019-12-18 15:59:30 UTC  
> Url: https://github.com/boostorg/boost/pull/356#discussion_r359340355  

Please don't commit commented code. If you want to keep it, commit with it and then remove in next commit. So you can go back if ever required

> Username: ned14  
> Created_at: 2019-12-18 14:38:31 UTC  
> Updated_at: 2019-12-18 15:59:30 UTC  
> Url: https://github.com/boostorg/boost/pull/356#discussion_r359378633  

I'm not modifying the embedded pcpp.


---

## Review 2 [Commented]

> Username: ned14  
> Created_at: 2019-12-18 14:42:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/boost/pull/356#pullrequestreview-334018640  

Thanks for the detailed feedback. Re: whether to do an actual full C99 preprocessor pass, well, why not in the end? Besides, a full programmable tokenising preprocessor opens the door to lots of other very cool library checks in the future. You have complete access to a token stream, you can do far more powerful checks than regex ever could.

---

## Comment 3

> Username: Flamefire  
> Created_at: 2019-12-18 14:46:34 UTC  
> Url: https://github.com/boostorg/boost/pull/356#issuecomment-567062004  

> Re: whether to do an actual full C99 preprocessor pass, well, why not in the end?  
  
Because: It is very complex and hence can contain hard to find bugs. I didn't bother reviewing the implementation because it was to much... When you are done: Can you run it over the whole of Boosts headers and see if any warning/error comes up related to the PP?

---

## Comment 4

> Username: Flamefire  
> Created_at: 2019-12-18 14:48:48 UTC  
> Url: https://github.com/boostorg/boost/pull/356#issuecomment-567063018  

Ah this is from https://github.com/ned14/pcpp? Ok then :+1:

---

## Review 5 [Commented]

> Username: Flamefire  
> Created_at: 2019-12-18 14:56:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/boost/pull/356#pullrequestreview-334028818  

📁 status/boost_check_library.py

```diff
 113 |+             badmacros = {}
 114 |+             for root,dirs,files in os.walk(libdir):
 115 |+                 if (os.path.sep + 'detail' + os.path.sep) in root or root.endswith(os.path.sep + 'detail'):
```

> Username: Flamefire  
> Created_at: 2019-12-18 14:56:00 UTC  
> Updated_at: 2019-12-18 15:59:30 UTC  
> Url: https://github.com/boostorg/boost/pull/356#discussion_r359388906  

Better:  
`if 'detail' in dirs: dirs.remove('detail')`  
  
This avoids going down into the detail headers completely speeding up the traversal

> Username: ned14  
> Created_at: 2019-12-18 14:58:53 UTC  
> Updated_at: 2019-12-18 15:59:30 UTC  
> Url: https://github.com/boostorg/boost/pull/356#discussion_r359390698  

I don't think what you propose does that. We never use dirs in fact.

> Username: Flamefire  
> Created_at: 2019-12-18 15:01:58 UTC  
> Updated_at: 2019-12-18 15:59:30 UTC  
> Url: https://github.com/boostorg/boost/pull/356#discussion_r359392429  

It does: https://docs.python.org/3/library/os.html#os.walk

> Username: Flamefire  
> Created_at: 2019-12-18 15:02:31 UTC  
> Updated_at: 2019-12-18 15:59:30 UTC  
> Url: https://github.com/boostorg/boost/pull/356#discussion_r359392780  

> When topdown is True, the caller can modify the dirnames list in-place (perhaps using del or slice assignment), and walk() will only recurse into the subdirectories whose names remain in dirnames; this can be used to prune the search,

> Username: ned14  
> Created_at: 2019-12-18 15:56:13 UTC  
> Updated_at: 2019-12-18 15:59:30 UTC  
> Url: https://github.com/boostorg/boost/pull/356#discussion_r359424866  

Well, you learn something every day. Thanks.


---

## Comment 6

> Username: ned14  
> Created_at: 2019-12-18 14:56:48 UTC  
> Url: https://github.com/boostorg/boost/pull/356#issuecomment-567066734  

Yes it's a straight pull from the pcpp 1.21 release. I put a note to that effect, and its copyright notice, into the Readme file inside the embedded pcpp.  
  
In terms of parsing Boost headers, apart from its lack of proper expression evaluation (errors from which I have suppressed), it does fine. It won't produce entirely correct output for when using Boost.VMD, but last time I checked it didn't barf when being fed them.

---

## Comment 7

> Username: Flamefire  
> Created_at: 2019-12-18 15:00:43 UTC  
> Url: https://github.com/boostorg/boost/pull/356#issuecomment-567068548  

I'm curious: How far does it go? Will `p.macros` contain the invalid macro from:  
  
```  
#ifdef BOOST_NOT_DEFINED  
  #define NOT_DEFINED  
#endif  
```  
  
Or does it just parse the tokens and hence return both macros? What about `#if defined(FOO) && (FOO > BAR)`? Does it get both macros?

---

## Comment 8

> Username: ned14  
> Created_at: 2019-12-18 15:25:04 UTC  
> Url: https://github.com/boostorg/boost/pull/356#issuecomment-567078825  

Changed to no longer execute preprocessor logic or undefining. That's for the comment on that, I had not thought of that.

---

## Comment 9

> Username: glenfe  
> Created_at: 2020-04-09 23:29:50 UTC  
> Url: https://github.com/boostorg/boost/pull/356#issuecomment-611801304  

This solution is too heavyweight - e.g. importing a YACC and a PP implementation just for this. This isn't a problem for new code - i.e. most or all of the offending macros were either historical or just copying the existing convention in that library - so I don't think we need something so extensive to catch these. Having an automated check would be nice, but something simpler would do (e.g. the same kind of thing we have for detecting missing Copyrights and License text in files).

---
