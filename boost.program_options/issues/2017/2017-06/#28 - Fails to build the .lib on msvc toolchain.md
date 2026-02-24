# #28 - Fails to build the .lib on msvc toolchain [Closed]

> Username: vinniefalco  
> Created at: 2017-06-25 14:31:06 UTC  
> Updated at: 2017-06-25 19:30:38 UTC  
> Closed at: 2017-06-25 18:38:42 UTC  
> Url: https://github.com/boostorg/program_options/issues/28  

**master** branch of the superproject (https://github.com/boostorg/boost/commit/43468c7a8492ab00c6f0bfb993acfe9e1dd5e844) fails to produce the .lib files for program options, causing the build of my own library tests to fail:  
  
https://ci.appveyor.com/project/vinniefalco/beast/build/master%20(%231328)  
>msvc.link libs\beast\bin\example\echo-op\msvc-14.0\debug\threading-multi\echo-op.exe  
LINK : fatal error LNK1181: cannot open input file 'bin.v2\libs\program_options\build\msvc-14.0\debug\threading-multi\boost_program_options-vc140-mt-gd-1_65.lib  
  
Performing `git checkout boost-1.64.0` resolves the issue. The problem has been bisected with certainty, indicating these defective commits:  
  
Superproject: https://github.com/boostorg/boost/commit/025ccc062abf21cf44d500def72fba5ff6f08966  
  
program_options: https://github.com/boostorg/program_options/commit/1f9413f532e58b82d12e9185c2eb8bc45612914c  
  
Furthermore we have identified another critical issue, @vprus has not yet joined the `#boost` channel on **cpp-lang** Slack: http://cpplang.diegostamigni.com/ Please resolve this!

---

## Comment 1

> Username: vprus  
> Created at: 2017-06-25 18:38:42 UTC  
> Url: https://github.com/boostorg/program_options/issues/28#issuecomment-310919934  

This should be fixed in https://github.com/boostorg/boost/commit/a696afede9da8dbf0e3084ad07585148903bfaca - note that it is superproject commit.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-06-25 18:42:03 UTC  
> Url: https://github.com/boostorg/program_options/issues/28#issuecomment-310920133  

And what about the second half of the issue?

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-06-25 18:59:01 UTC  
> Url: https://github.com/boostorg/program_options/issues/28#issuecomment-310921145  

Confirmed, original defect is fixed:  
https://ci.appveyor.com/project/vinniefalco/beast/build/v68%20(%231340)

---

## Comment 4

> Username: vprus  
> Created at: 2017-06-25 19:27:14 UTC  
> Url: https://github.com/boostorg/program_options/issues/28#issuecomment-310922702  

Sorry, what is the second half?  
  
On Sun, Jun 25, 2017, 21:59 Vinnie Falco <notifications@github.com> wrote:  
  
> Confirmed, original defect is fixed:  
> https://ci.appveyor.com/project/vinniefalco/beast/build/v68%20(%231340)  
>  
> —  
> You are receiving this because you modified the open/close state.  
>  
>  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/program_options/issues/28#issuecomment-310921145>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AANye8US6dUdNfsoI6O9MHnLWMuVaoeBks5sHq31gaJpZM4OEnyd>  
> .  
>  
--   
Vladimir Prus  
http://vladimirprus.com

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-06-25 19:30:38 UTC  
> Url: https://github.com/boostorg/program_options/issues/28#issuecomment-310922867  

This is the second half:  
>...we have identified another critical issue, @vprus has not yet joined the #boost channel on cpp-lang Slack: http://cpplang.diegostamigni.com/ Please resolve this!  
  
See:   
![image](https://user-images.githubusercontent.com/1503976/27519114-16b9a2d0-59a2-11e7-953f-9d7bdb247732.png)
