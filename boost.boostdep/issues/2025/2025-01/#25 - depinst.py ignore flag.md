# #25 - depinst.py ignore flag [Open]

> Username: sdarwin  
> Created at: 2025-01-14 22:49:39 UTC  
> Updated at: 2025-01-15 01:38:09 UTC  
> Url: https://github.com/boostorg/boostdep/issues/25  

How does the --ignore flag work in `depinst.py`?     
  
Example:  
  
```  
git clone https://github.com/boostorg/boost  
cd boost  
git submodule update --init tools/boostdep  
git submodule update --init tools/quickbook  
python3 tools/boostdep/depinst/depinst.py --ignore unordered ../tools/quickbook  
```  
  
Notice `--ignore unordered`  
  
The verbose output of `depinst.py -vv` shows:  
  
`-N: ['unordered']`  
  
Then, would it be expected if the submodule `unordered` is downloaded/cloned?  It is.

---

## Comment 1

> Username: pdimov  
> Created at: 2025-01-15 00:37:09 UTC  
> Url: https://github.com/boostorg/boostdep/issues/25#issuecomment-2591391190  

`--ignore` ignores direct dependencies, but not indirect ones. This might be why.

---

## Comment 2

> Username: sdarwin  
> Created at: 2025-01-15 00:52:26 UTC  
> Url: https://github.com/boostorg/boostdep/issues/25#issuecomment-2591406569  

When building docs, quickbook is necessary.   So, run this:  
  
```  
git submodule update --init tools/quickbook  
python3 tools/boostdep/depinst/depinst.py ../tools/quickbook  
```  
  
That installs very many boost libraries.   
  
What if one of those is an environment where you are testing docs and trying to build them.    Such as `boostorg/unordered`.      
  
`depinst.py` clobbers the directory, overwriting it.  
  
It would be nice for `--ignore` to also skip indirect dependencies.  
  
I have implemented a work-around, for now. rebuild/recopy the lib directory after depinst runs.

---

## Comment 3

> Username: pdimov  
> Created at: 2025-01-15 00:57:21 UTC  
> Url: https://github.com/boostorg/boostdep/issues/25#issuecomment-2591411713  

depinst does `git submodule update`, it doesn't clobber. Normally there's nothing in `libs/unordered` before you run depinst (because you run depinst to perform the necessary submodule updates.)  
  
What exactly is the scenario in which there's already something in libs/unordered, and it's not what a submodule update would put there?

---

## Comment 4

> Username: sdarwin  
> Created at: 2025-01-15 01:06:10 UTC  
> Url: https://github.com/boostorg/boostdep/issues/25#issuecomment-2591420662  

> What exactly is the scenario  
  
Developing, testing, and building the documentation for a boost library...   when it's not necessarily matching the exact commit which is registered in the superproject.  
  
For example, building docs for a boost library from code that has been submitted via a pull request.  
  
Or, doing forward development, adding more features to existing documents, in a local environment.  
  
Many build scripts seem to expect the target files are in libs/unordered (or libs/library_name).  That is where they usually go.   So it's a natural location for modifying and testing the doc/ files.

---

## Comment 5

> Username: sdarwin  
> Created at: 2025-01-15 01:13:03 UTC  
> Url: https://github.com/boostorg/boostdep/issues/25#issuecomment-2591428815  

Maybe, what I just wrote, is not strictly necessary...   You can have a repository, separate from the superproject, not in the libs folder, and then it would not hit this problem.     
But for test cases I have been working with, using the structure of the superproject organization is convenient.

---

## Comment 6

> Username: pdimov  
> Created at: 2025-01-15 01:18:03 UTC  
> Url: https://github.com/boostorg/boostdep/issues/25#issuecomment-2591434273  

Sure but normally when you do  
  
```  
git clone https://github.com/boostorg/boost  
cd boost  
git submodule update --init tools/boostdep  
git submodule update --init tools/quickbook  
```  
  
there's nothing in libs/unordered. For there to be something, you have to put it there first.  
  
So, instead of putting something there before running  
  
```  
python3 tools/boostdep/depinst/depinst.py ../tools/quickbook  
```  
  
put it there after running it.

---

## Comment 7

> Username: sdarwin  
> Created at: 2025-01-15 01:26:45 UTC  
> Url: https://github.com/boostorg/boostdep/issues/25#issuecomment-2591442892  

>  after running it.  
  
Yes, right. Got it.  
  
It's creating a strict ordered dependency though.   1-2-3. That's fine for CI in github actions, and then the container is destroyed.     
  
Some of the scripts that I wrote though, are aimed at local development, and repeated testing. The order of events isn't so determined.

---

## Comment 8

> Username: pdimov  
> Created at: 2025-01-15 01:38:07 UTC  
> Url: https://github.com/boostorg/boostdep/issues/25#issuecomment-2591454107  

If we're to add this functionality, we'll need another option for it (e.g. -P --preserve). The existing --ignore means something else - it's used if you want to ignore a direct dependency including all its indirect dependencies, not when you want to preserve a specific directory.
