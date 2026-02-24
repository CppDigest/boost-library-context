# #423 help: Print help for local modules as well. [Merged]

> Username: tee3  
> Created at: 2019-04-03 15:55:24 UTC  
> Updated at: 2021-10-02 20:59:34 UTC  
> Merged at: 2020-03-07 13:39:19 UTC  
> Closed at: 2020-03-07 13:39:19 UTC  
> Url: https://github.com/boostorg/build/pull/423  

This pull request adds support for help for modules in the current directory.  I added this because using `--help-internal` did not list the module and `--help module` with did not show the help.  I don't know if this is the best approach to adding this functionality, but adding new options would have required changing a lot of code in `help.jam` and `doc.jam`.  It was not simple to just add something there without duplicating some complicated code.  
  
Both the `--help-internal` and `--help module` now search the current directory for modules.  
  
I originally thought that adding the current directory to `BOOST_BUILD_PATH` would solve this problem, but it did not seem to.  
  
I tried to poke the current directory into the `BOOST_BUILD_PATH` in each of `user-config.jam`, `project-config.jam`, and`site-config.jam`.  This works for importing the local module, but not when using `--help`.  
  
```  
{  
  import modules ;  
  
  local x = [ modules.peek : BOOST_BUILD_PATH ] ;  
  modules.poke : BOOST_BUILD_PATH : . $(x) ;  
}  
```  
  
I'm was sure this is the best way to approach this, so I have a few questions.  
  
1. Is there a different way to do this?  Am I totally off base?  
  
2. Should modifying `BOOST_BUILD_PATH` locally work?  If so, should I be doing that another way?  
  
3. Should we add new options such as `--help-local` and`--help-internal-local` instead of modifying the search path of `--help` and `--help-internal`?  
  
4. Should we rework the code of `help.jam` and `doc.jam` to be a bit more flexible?

---

## Comment 1

> Username: swatanabe  
> Created_at: 2019-04-03 16:00:35 UTC  
> Url: https://github.com/boostorg/build/pull/423#issuecomment-479552414  

AMDG  
  
On 4/3/19 9:55 AM, tee3 wrote:  
> This pull request adds support for help for modules in the current directory.  <snip>  
> I originally thought that adding the current directory to `BOOST_BUILD_PATH` would solve this problem, but it did not seem to.  
>   
  
It should work if BOOST_BUILD_PATH is set in the environment.  
  
> I tried to poke the current directory into the `BOOST_BUILD_PATH` in each of `user-config.jam`, `project-config.jam`, and`site-config.jam`.  This works for importing the local module, but not when using `--help`.  
>   
  
That's because --help bypasses the normal  
build process including loading config files.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: tee3  
> Created_at: 2019-04-03 17:09:06 UTC  
> Url: https://github.com/boostorg/build/pull/423#issuecomment-479577385  

@swatanabe In your opinion, should I close this pull request?  
  
> It should work if BOOST_BUILD_PATH is set in the environment.  
  
Thanks.  I misunderstood how that environment variable worked and I thought setting it would override the default `BOOST_BUILD_PATH`.  It is not too bad to require that it is set it this way, though it's one more thing to describe to users.  
  
> That's because --help bypasses the normal build process including loading config files.  
  
I'm assuming this is in part to avoid errors that occur if it is run where this is no Boost.Build system.  Are there other reasons for this?  Is there a way to do this without bypassing the normal process?

---

## Comment 3

> Username: swatanabe  
> Created_at: 2019-04-03 17:26:48 UTC  
> Url: https://github.com/boostorg/build/pull/423#issuecomment-479583649  

AMDG  
  
On 4/3/19 11:09 AM, tee3 wrote:  
> @swatanabe In your opinion, should I close this pull request?  
>   
  
No.  I think it's probably a good idea for  
--help to use the same path that's used  
for finding modules.  Jamfiles automatically  
get their own directory added to BOOST_BUILD_PATH  
when they import things.  Adding the current  
working directory isn't prefect, but it is an  
improvement.  
  
>> It should work if BOOST_BUILD_PATH is set in the environment.  
>   
> Thanks.  I misunderstood how that environment variable worked and I thought setting it would override the default `BOOST_BUILD_PATH`.  It is not too bad to require that it is set it this way, though it's one more thing to describe to users.  
>   
>> That's because --help bypasses the normal build process including loading config files.  
>   
> I'm assuming this is in part to avoid errors that occur if it is run where this is no Boost.Build system.  Are there other reasons for this?  
  
The main reason is that it's not necessary.  
We don't want to build anything.  The config  
files normally don't do anything that would  
affect the behavior of --help.  
  
>  Is there a way to do this without bypassing the normal process?  
>   
  
--help gets processed very early via a generic  
option processing hook, so I don't think this  
would be easy.  
  
In Christ,  
Steven Watanabe

---

## Comment 4

> Username: github-actions[bot]  
> Created_at: 2021-10-02 20:59:33 UTC  
> Url: https://github.com/boostorg/build/pull/423#issuecomment-932819664  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
