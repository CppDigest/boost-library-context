# #558 - bootstrap.sh report error [Open]

> Username: runner365  
> Created at: 2021-09-09 10:36:56 UTC  
> Updated at: 2026-02-02 17:04:22 UTC  
> Url: https://github.com/boostorg/boost/issues/558  

./bootstrap.sh  
./bootstrap.sh: line 199: ./tools/build/src/engine/build.sh: No such file or directory  
Building B2 engine..  
./bootstrap.sh: line 229: ./tools/build/src/engine/build.sh: No such file or directory  
  
Failed to build B2 build engine  
  
mac.

---

## Comment 1

> Username: mclow  
> Created at: 2021-09-09 20:49:38 UTC  
> Url: https://github.com/boostorg/boost/issues/558#issuecomment-916426068  

I just looked in the 1.77.0 release.  That file is present.  
I checked the 1.76.0 release.  It's there too.  
  
Where did you get your boost installation? (and what version, etc).

---

## Comment 2

> Username: ykarazia  
> Created at: 2021-09-14 22:55:42 UTC  
> Url: https://github.com/boostorg/boost/issues/558#issuecomment-919567833  

I reproduced the same issue.  
  
Cloned repository.  
  
➜  boost git:(master) git describe  
boost-1.71.0.beta1-1637-g164ae2115  
  
./bootstrap.sh: line 199: ./tools/build/src/engine/build.sh: No such file or directory  
Building B2 engine..  
./bootstrap.sh: line 229: ./tools/build/src/engine/build.sh: No such file or directory  
  
Failed to build B2 build engine  
  
----  
  
➜  boost git:(master) sw_vers  
ProductName:	macOS  
ProductVersion:	11.5.2  
BuildVersion:	20G95  
  
If I sort it out, I will post back here.

---

## Comment 3

> Username: ykarazia  
> Created at: 2021-09-14 23:27:44 UTC  
> Url: https://github.com/boostorg/boost/issues/558#issuecomment-919581386  

The solution for me was to read the instructions :(  
  
It's possible we did the same thing.  
  
You need to recursive clone before running the bootstrap.  
  
Found "Install from Git" instructions here:  
https://github.com/boostorg/wiki/wiki/Getting-Started%3A-Overview  
  
@runner365 , give it a go and close the issue if this solves the problem. I may be a good idea to check for this missing file and recommend the --recursive flag on the clone, or a printout which points to the instructions for those of us who never read instructions to begin with :D

---

## Comment 4

> Username: bkmgit  
> Created at: 2021-09-29 10:10:29 UTC  
> Url: https://github.com/boostorg/boost/issues/558#issuecomment-930037801  

This seems to happen on the releases provided on GitHub (for example https://github.com/boostorg/boost/archive/refs/tags/boost-1.77.0.tar.gz ), but is not an issue for the releases on JFrog, for example https://boostorg.jfrog.io/ui/native/main/release/1.77.0/source/

---

## Comment 5

> Username: pkeir  
> Created at: 2022-12-10 18:56:16 UTC  
> Updated at: 2022-12-10 18:57:00 UTC  
> Url: https://github.com/boostorg/boost/issues/558#issuecomment-1345361919  

Links from https://www.boost.org/users/history/ now point to directory links such as https://boostorg.jfrog.io/artifactory/main/release/1.77.0/source. For example: https://boostorg.jfrog.io/artifactory/main/release/1.77.0/source/boost_1_77_0.tar.gz

---

## Comment 6

> Username: andim2  
> Created at: 2026-02-02 17:04:22 UTC  
> Url: https://github.com/boostorg/boost/issues/558#issuecomment-3836520098  

Usability:  
while @ykarazia provided some crucial resolution steps, _IMHO_ current repo state is insufficient (somewhat broken usability).  
As a developer, I'd often like to grab _git_ repo state and start from that - rather than having to grab a hefty (150MB+) "hardcoded" / "fixed" this-version archive blob from a specific download _URL_.  
  
Yet when doing so, and following the  
docs chain  
INSTALL -> index.html -> more/getting_started/index.html -> https://www.boost.org/doc/user-guide/getting-started.html  
, and then trying to do `bootstrap.sh` things, then  
one will get:  
  
> $ ./bootstrap.sh  
> ./bootstrap.sh: 1: ./tools/build/src/engine/build.sh: not found  
> Building B2 engine..  
> ./bootstrap.sh: 229: ./tools/build/src/engine/build.sh: not found  
>   
> Failed to build B2 build engine  
  
in case one somehow has failed to _actively_ read (or even notice - different tab...) the _git_ tab part (at "Or you can streamline the whole process directly from the command line:").  
  
So, I'd think that `bootstrap.sh` has insufficient usability.  
  
Since `bootstrap.sh` is root scope (i.e., authoritative scope), it quite possibly should be providing something like  
if !tools/build then  
  error "tools/build infrastructure missing - looks like you grabbed the repository yet failed to initialize the complete super-project - please see User Guide, Getting Started section's git tab for full instructions."  
  
(though this then still does not answer the question of  
how to achieve going from an _existing_ plain repo checkout state to full super-project state)  
  
One might argue that  
having such very specific handling within `bootstrap.sh` is not appropriate -  
however `bootstrap.sh` _is_ top-level / root handling, thus  
it likely _should_ provide some set of such rather specific sub scope instructions.  
Or it perhaps should handover to a corresponding script in `tools/build` sub project root scope (which would then handle implementation details internals: path currently `src/engine/build.sh` ).  
  
Thank you!
