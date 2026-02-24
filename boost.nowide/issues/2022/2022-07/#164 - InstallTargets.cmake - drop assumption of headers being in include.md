# #164 - InstallTargets.cmake : drop assumption of headers being in include/ [Closed]

> Username: eyalroz  
> Created at: 2022-07-11 19:28:00 UTC  
> Updated at: 2022-07-12 08:44:29 UTC  
> Closed at: 2022-07-12 08:44:29 UTC  
> Url: https://github.com/boostorg/nowide/issues/164  

Since you've suggested this CMake module as something others might use, perhaps you can generalize it to work with merged-includes-and-sources, or at least with header-only libraries where the headers are under `src/`.

---

## Comment 1

> Username: Flamefire  
> Created at: 2022-07-11 20:12:29 UTC  
> Url: https://github.com/boostorg/nowide/issues/164#issuecomment-1180822004  

IMO having to add a folder called `include` which is to be added to the include search paths is a common enough convention.  
Header-only libraries where headers are under `src` can simply rename that folder to `include` as `src` is usually understood as a folder for files to compile. If they really insist not to do that, they can change that one line in the module.  
For merged includes and sources a module like that isn't possible as merged includes requires one to more or less (can use glob) list each header to be installed.  
  
So in summary every simplification builds on assumptions and/or conventions. For environments where this doesn't hold and where those can't be introduced this module is a good starter.  
  
For context: Were you referring to [this](https://gitlab.kitware.com/cmake/cmake/-/issues/20208#note_701489) discussion in the CMake repo? Note that I mentioned there that this is meant for "conventional layouts" and that there are better ways than this in newer CMake versions (see the linked issues/MRs in that discussion)

---

## Comment 2

> Username: eyalroz  
> Created at: 2022-07-11 20:18:10 UTC  
> Updated at: 2022-07-11 20:18:15 UTC  
> Url: https://github.com/boostorg/nowide/issues/164#issuecomment-1180827504  

> is a common enough convention.  
  
It's quite common; but having the includes with the source directories is also very common and conventional ...  
  
> Were you referring to [this](https://gitlab.kitware.com/cmake/cmake/-/issues/20208#note_701489) discussion in the CMake repo?  
  
Yes :-)

---

## Comment 3

> Username: Flamefire  
> Created at: 2022-07-12 08:44:29 UTC  
> Url: https://github.com/boostorg/nowide/issues/164#issuecomment-1181490083  

I understand.  
However I do not have any intention to change this module. It was never meant to be a universal module, just one solution given certain constraints.  
It works for the intended use case and in that discussion it was an example on how to simplify the CMake workflow when following one (IMO the most common) convention for the directory layout. It has since been superseded by CMakes "FILE_SET" mechanism which allows to add headers to a target and install those.
