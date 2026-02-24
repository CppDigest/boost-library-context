# #23 All Options help. [Closed]

> Username: frenchtoast747  
> Created at: 2014-07-21 22:41:11 UTC  
> Updated at: 2021-10-02 22:35:49 UTC  
> Closed at: 2014-08-14 13:19:17 UTC  
> Url: https://github.com/boostorg/build/pull/23  

The proposed changes allow for options plugins within the `options` directory to automatically contain command-line help by creating a variable whose name starts with the prefix `.options.` and is also preceded by a variable comment in the module level namespace. The option will also contain a description when the variable `.option-description` is defined and also preceded by a comment.  
  
For example:  
  
Say there is a file called `tests.jam` within the `options` directory. Within the `tests` module is the following:  
  
```  
import option ;  
  
# These are the options for the Tests option plugin  
.option-description = Tests Options ;  
  
# Continue running even in the event of a failure.  
.option.--tests-no-fail-on-run ?= ;  
  
# Passes the -i flag to the Tests executable.  
.option.--tests-review ?= ;  
  
# Opens Visual Studio at the Tests entry point.  
.option.--tests-dbg ?= ;  
  
# Passes the value given to the Tests executable.  
.option.--tests-cmd ?= ;  
  
rule process (  
    command # The option.  
    : values * # The values, starting after the "=".  
    )  
{  
    option.set $(command) : $(values) ;  
}  
```  
  
Running the command `bjam --help-options` will output all of the help options associated with `bjam` as well as any documentation for each plugin within the `options` directory:  
  
```  
Boost.Build Usage:  
  
  b2 [ options... ] targets...  
  
  * -a; Build all targets, even if they are current.  
  * -fx; Read 'x' as the Jamfile for building instead of searching for the  
    Boost.Build system.  
  * -jx; Run up to 'x' commands concurrently.  
  * -n; Do not execute build commands. Instead print out the commands as they  
    would be executed if building.  
  * -ox; Output the used build commands to file 'x'.  
  * -q; Quit as soon as a build failure is encountered. Without this option  
    Boost.Jam will continue building as many targets as it can.  
  * -sx=y; Sets a Jam variable 'x' to the value 'y', overriding any value that  
    variable would have from the environment.  
  * -tx; Rebuild the target 'x', even if it is up-to-date.  
  * -v; Display the version of b2.  
  * --x; Any option not explicitly handled by Boost.Build remains available to  
    build scripts using the 'ARGV' variable.  
  * --abbreviate-paths; Use abbreviated paths for targets.  
  * --hash; Shorten target paths by using an MD5 hash.  
  * -dn; Enables output of diagnostic messages. The debug level 'n' and all  
    below it are enabled by this option.  
  * -d+n; Enables output of diagnostic messages. Only the output for debug  
    level 'n' is enabled.  
  
Debug Levels:  
  
  Each debug level shows a different set of information. Usually with higher  
  levels producing more verbose information. The following levels are supported:  
  
  * 0; Turn off all diagnostic output. Only errors are reported.  
  * 1; Show the actions taken for building targets, as they are executed.  
  * 2; Show quiet actions and display all action text, as they are executed.  
  * 3; Show dependency analysis, and target/source timestamps/paths.  
  * 4; Show arguments of shell invocations.  
  * 5; Show rule invocations and variable expansions.  
  * 6; Show directory/header file/archive scans, and attempts at binding to  
    targets.  
  * 7; Show variable settings.  
  * 8; Show variable fetches, variable expansions, and evaluation of 'if'  
    expressions.  
  * 9; Show variable manipulation, scanner tokens, and memory usage.  
  * 10; Show execution times for rules.  
  * 11; Show parsing progress of Jamfiles.  
  * 12; Show graph for target dependencies.  
  * 13; Show changes in target status (fate).  
  
Tests Options:  
    These are the options for the Tests option plugin  
  
    * --tests-cmd: Passes the value given to the Tests executable. Default is disabled.  
    * --tests-dbg: Opens Visual Studio at the Tests entry point. Default is disabled.  
    * --tests-no-fail-on-run: Continue running even in the event of a failure. Default is disabled.  
    * --tests-review: Passes the -i flag to the Tests executable. Default is disabled.  
```  
  
And because all options are prefixed by the same name as the options module name, `tests`, the process rule will automatically get called during initialization.

---

## Comment 1

> Username: frenchtoast747  
> Created_at: 2014-07-21 22:42:20 UTC  
> Url: https://github.com/boostorg/build/pull/23#issuecomment-49676212  

This change should be linked with this question on StackOverflow:  
http://stackoverflow.com/q/24213484/1164082

---

## Comment 2

> Username: vprus  
> Created_at: 2014-07-25 05:56:06 UTC  
> Url: https://github.com/boostorg/build/pull/23#issuecomment-50110691  

Thanks for your submission. I was on a vacation half of this week, so still catching up on stuff. I'll try to review your pull request in next few days.

---

## Comment 3

> Username: vprus  
> Created_at: 2014-08-14 13:19:17 UTC  
> Url: https://github.com/boostorg/build/pull/23#issuecomment-52181585  

Thanks for the patches! I've merged these (squashing commits together).

---

## Comment 4

> Username: frenchtoast747  
> Created_at: 2014-08-14 13:54:36 UTC  
> Url: https://github.com/boostorg/build/pull/23#issuecomment-52185758  

Thank you for the merge!

---
