# #492 - Suggestion: make debug-store=database the default on Windows [Open]

> Username: thebrandre  
> Created at: 2019-10-12 14:55:43 UTC  
> Updated at: 2021-05-29 16:22:48 UTC  
> Url: https://github.com/boostorg/build/issues/492  

Refers to 1.71.  
  
The default [Debug Information Format](https://docs.microsoft.com/en-us/cpp/build/reference/z7-zi-zi-debug-information-format?view=vs-2019) of boost on Windows is /Z7.  
From my point of view, there are quite a number of reasons to use /Zi (debug-store=database in terms of boost build) instead:  
  
1. File size: for all the configurations of boost we have in our organization, the total file size of the boost binaries decreases from 4 GB to 2.5 GB when enabling debug-store=database.  
Some examples:  
    For variant=debug and address-model=64:  
    debug-store=database: 458 MB of lib + 90 MB of pdb = 549 MB in total  
    default (/Z7): 706 MB  
      
    For variant=debug address-model=32:  
    debug-store=database: 347 MB of lib + 93 MB of pdb = 440 MB in total  
    default (/Z7): 597 MB  
  
    For variant=release address-model=64:  
    debug-store=database: 412 MB of lib + 89 MB of pdb = 501 MB in total  
    /Z7: 654 MB  
    default (no debug info): 223 MB  
Here's a (admittedly ugly) overview that also shows which libraries are contributing to the binary size:  
![overview_binary_sizes](https://user-images.githubusercontent.com/18500984/66702832-2bb23300-ed0c-11e9-9827-eb4b51dab890.png)  
  
2. Familiarity: everyone uses /Zi on Windows. cmake and Visual Studio (at least since Visual Studio 2010). I first encountered /Z7 after several years of development on Windows when I asked myself why the boost debug binaries are so insanely large and where on earth are my PDBs? As /Zi is the most common option elsewhere and only few people are familiar with boost build, they should get what they except without digging to deep.   
  
3. Deployment: There is no convenient and easily available [strip](https://linux.die.net/man/1/strip) command on Windows to get rid of the debug information embedded in the binary (at least that I know of). But with /Zi, you simply don't deploy the PDB files.  
  
4. Technical point of view: the [pch issue](https://github.com/boostorg/build/issues/269#issuecomment-534245308) is only hidden in /Z7 because one would have to deploy the obj files according to the [Windows docs](https://docs.microsoft.com/en-us/cpp/build/reference/z7-zi-zi-debug-information-format?view=vs-2019).  
  
5. Reliability: /Zi is not exactly maintained by boost at the moment as seen by the [issues](https://github.com/boostorg/build/issues/269#issuecomment-534245308) referred to earlier. Not many people know boost build and look through all the options it provides. So only the defaults are well-tested.  
  
There are of course downsides I can think of (not many):  
1. It might require a lot of testing.  
2. One would have to fix these minor (?) [issues](https://github.com/boostorg/build/issues/269#issuecomment-534245308) (same link as before).   
  
Any thoughts on that?

---

## Comment 1

> Username: mscottmueller  
> Created at: 2021-04-30 05:06:55 UTC  
> Url: https://github.com/boostorg/build/issues/492#issuecomment-829841255  

Another requirement of this could be to get the PDBs deployed to the target location, or wherever the .libs and .dlls end up. This also would help with things like Binskim, which requires access to debug information not stored within the binaries with /Z7, and would also open the door to addressing technologies like SourceLink.

---

## Comment 2

> Username: stale[bot]  
> Created at: 2021-05-29 16:22:18 UTC  
> Url: https://github.com/boostorg/build/issues/492#issuecomment-850859573  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
