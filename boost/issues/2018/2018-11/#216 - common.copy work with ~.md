# #216 - common.copy work with ~ [Closed]

> Username: logbob0401  
> Created at: 2018-11-04 04:25:07 UTC  
> Updated at: 2020-11-13 18:14:12 UTC  
> Closed at: 2019-06-26 15:11:58 UTC  
> Url: https://github.com/boostorg/boost/issues/216  

When i install boost 1.6.8 with:  
$ ./bootstrap.sh --prefix=~/somewhere  
$ ./b2 install  
then i get a subdirectory  named ~/somewhere  at working directory.

---

## Comment 1

> Username: jeking3  
> Created at: 2018-11-27 00:20:38 UTC  
> Url: https://github.com/boostorg/boost/issues/216#issuecomment-441850362  

I can verify the behavior.  The `project-config.jam` gets `~/somewhere` instead of the expanded path.  Perhaps bootstrap.sh should be using `readlink -f $PREFIX` to expand it.

---

## Comment 2

> Username: mjcaisse  
> Created at: 2019-05-31 00:14:23 UTC  
> Url: https://github.com/boostorg/boost/issues/216#issuecomment-497530023  

I assume you are using `bash` ?  
  
If so, this is the expected behaviour for the shell. It will not expand tilde because `--prefix=` does not appear to be a simple variable assignment.  
  
> Bash also performs tilde expansion on words satisfying the conditions of variable assignments (as described above under PARAMETERS) when they appear as arguments to simple commands. Bash does not do this, except for the declaration commands listed above, when in posix mode.

---

## Comment 3

> Username: mjcaisse  
> Created at: 2019-06-26 15:11:58 UTC  
> Url: https://github.com/boostorg/boost/issues/216#issuecomment-505919117  

This is the behaviour of the shell.

---

## Comment 4

> Username: yiyunliu  
> Created at: 2020-11-13 18:14:12 UTC  
> Url: https://github.com/boostorg/boost/issues/216#issuecomment-726933336  

It is embarrassing, but I accidentally removed my home directory because of this. Here're the steps:  
```  
$ ./bootstrap.sh --prefix=~/.local  
$ ./b2 install  
```  
At first, I realized `~/.local/` did not have boost installed. Then I checked the build directory and found `"~"` was created. A natural thing I did (it was midnight and it never appeared to me that this would ever happen) was removing `"~"` with: `rm -r ~`.  
I agree that not expanding `~` is the behavior of the shell, but many existing tools like cmake does the expansion on its own. I'm used to type `~/` and it just works most of the time and is easier than `$HOME/`  
I've learned my lesson. I started using snapshots and doing backup daily, but I believe simple improvements can be made to avoid this kind of tragedy from happening. I can't think of a case where someone would ever want to create a directory named "~" on purpose.
