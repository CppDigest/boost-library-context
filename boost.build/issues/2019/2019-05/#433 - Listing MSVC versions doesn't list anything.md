# #433 - Listing MSVC versions doesn't list anything. [Closed]

> Username: Klaim  
> Created at: 2019-05-01 15:46:11 UTC  
> Updated at: 2019-05-01 16:54:42 UTC  
> Closed at: 2019-05-01 16:49:51 UTC  
> Url: https://github.com/boostorg/build/issues/433  

Observed, on Windows10, with Boost-1.70.0 b2 binary, built using the bootstrap script, using git-bash:  
  
```  
$ ./b2 --help msvc.versions  
...found 1 target...  
```  
  
Expected:  
Output MSVC 14.1 and 14.2 (which are both installed and available).

---

## Comment 1

> Username: swatanabe  
> Created at: 2019-05-01 16:32:52 UTC  
> Url: https://github.com/boostorg/build/issues/433#issuecomment-488334192  

AMDG  
  
On 5/1/19 9:46 AM, Joël Lamotte wrote:  
> Observed, on Windows10, with Boost-1.70.0 b2 binary, built using the bootstrap script, using git-bash:  
>   
> ```  
> $ ./b2 --help msvc.versions  
> ...found 1 target...  
> ```  
>   
> Expected:  
> Output MSVC 14.1 and 14.2 (which are both installed and available).  
>   
  
Why do you expect this?  There is no rule called msvc.versions.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: Klaim  
> Created at: 2019-05-01 16:42:39 UTC  
> Updated at: 2019-05-01 16:46:01 UTC  
> Url: https://github.com/boostorg/build/issues/433#issuecomment-488337157  

I first tried `./b2 --help` then `./b2 --help msvc` which in it's last paragraph specifies:  
  
```  
  
Module 'msvc' variables:  
  
  Use --help msvc.<variable-name> to get more information.  
  
  * .RM: Miscellaneous constants.  
  * .cpu-type-g5: Supported CPU types (only Itanium optimization options are  
    supported from VC++ 2005 on).  
  * .version-7.1toolkit-path: Visual C++ Toolkit 2003 does not store its  
    installation path in the registry.  
  * .version-alias-6: Version aliases.  
  * .versions: List of all registered configurations.  
  
```  
  
So I expected `./b2 --help msvc.versions` to "List of all registered configurations."  
  
Although my understanding of what a "registered configuration" is here might be different than the boost.build one.

---

## Comment 3

> Username: swatanabe  
> Created at: 2019-05-01 16:48:01 UTC  
> Url: https://github.com/boostorg/build/issues/433#issuecomment-488338710  

AMDG  
  
On 5/1/19 10:42 AM, Joël Lamotte wrote:  
> I first tried `./b2 --help` then `./b2 --help msvc` which in it's last paragraph specifies:  
>   
>>  
>> Module 'msvc' variables:  
>>  
>>   Use --help msvc.<variable-name> to get more information.  
>>  
>>   <snip>  
>>   * .versions: List of all registered configurations.  
>>  
>   
> So I expected `./b2 --help msvc.versions` to "List of all registered configurations."  
>   
> Although my understanding of what a "configuration" is here might be different than the boost.build one.  
>   
  
The msvc internal documentation is a bit of a mess.  These  
are all internal implementation details.  --help does not  
show the value of the variable, which is what you're looking  
for.  It shows the documentation describing the meaning of  
the variable.  Also, the name is ".versions" not "versions",  
which means that you need --help msvc..versions to see it.  
  
In Christ,  
Steven Watanabe

---

## Comment 4

> Username: Klaim  
> Created at: 2019-05-01 16:49:51 UTC  
> Url: https://github.com/boostorg/build/issues/433#issuecomment-488339304  

Thank you for claryfying. Is there some other command I'm not finding for listing the detected msvc versions?

---

## Comment 5

> Username: swatanabe  
> Created at: 2019-05-01 16:54:42 UTC  
> Url: https://github.com/boostorg/build/issues/433#issuecomment-488340736  

AMDG  
  
On 5/1/19 10:49 AM, Joël Lamotte wrote:  
> Thank you for claryfying. Is there some other command I'm not finding for listing the detected msvc versions?  
>   
  
There isn't a specific command, but it's part of the output  
of --debug-configuration.  
  
In Christ,  
Steven Watanabe
