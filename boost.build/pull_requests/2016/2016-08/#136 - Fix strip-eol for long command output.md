# #136 Fix strip-eol for long command output [Merged]

> Username: vmrob  
> Created at: 2016-08-15 22:32:48 UTC  
> Updated at: 2021-10-02 22:08:26 UTC  
> Merged at: 2017-05-09 03:00:47 UTC  
> Closed at: 2017-05-09 03:00:47 UTC  
> Url: https://github.com/boostorg/build/pull/136  

Previously, when shell command output exceeded the 1024 character  
buffer size, each chunk of output would be stripped. This had the  
undesirable effect of sometimes breaking compilation by splitting on  
whitespace boundaries. This patch addresses the issue by providing a  
string_rtrim function and utilizing the function on the output string  
instead of each buffered component.

---

## Comment 1

> Username: vmrob  
> Created_at: 2016-08-15 22:35:59 UTC  
> Updated_at: 2016-08-15 22:44:13 UTC  
> Url: https://github.com/boostorg/build/pull/136#issuecomment-239950141  

Please don't merge this until we can confirm that the `string_unit_test()` function runs properly. I was unable to determine how run the C-based unit tests and only confirmed that `python core_language.py darwin` and `python test_all.py darwin` worked on my machine.  
  
When defining DO_DIFF=1 prior to the run, I do get a failure in the builtin_glob_archive tests, but I suspect they are unrelated.

---

## Comment 2

> Username: vmrob  
> Created_at: 2016-09-06 19:42:33 UTC  
> Url: https://github.com/boostorg/build/pull/136#issuecomment-245066371  

Hi @vprus, any thoughts on merging this patch?

---

## Comment 3

> Username: vprus  
> Created_at: 2016-10-03 10:06:25 UTC  
> Url: https://github.com/boostorg/build/pull/136#issuecomment-251070546  

Hi Victor,  
  
which system was you testing on? Both on Unbuntu and Windows, the unit test appears to pass even without the code patch ;-)

---

## Comment 4

> Username: vmrob  
> Created_at: 2016-10-09 02:18:18 UTC  
> Updated_at: 2016-10-09 02:18:41 UTC  
> Url: https://github.com/boostorg/build/pull/136#issuecomment-252459907  

Very interesting! Please note that I had to add a new test to demonstrate the bug.  
  
I was previously testing on MacOS 10.11 and now MacOS 10.12. I can't easily downgrade my OS, but the following is what I see with test output before the patch **using the new test**:  
  
```  
$ python test_all.py darwin  
Note: skipping extra tests  
unit_tests                        : PASSED  
module_actions                    : PASSED  
startup_v2                        : PASSED  
core_d12                          : PASSED  
core_typecheck                    : PASSED  
core_delete_module                : PASSED  
core_language                     : FAILED  
core_arguments                    : PASSED  
core_varnames                     : PASSED  
core_import_module                : PASSED  
absolute_sources                  : PASSED  
alias                             : PASSED  
alternatives                      : PASSED  
bad_dirname                       : PASSED  
build_dir                         : PASSED  
build_file                        : PASSED  
build_no                          : PASSED  
builtin_echo                      : PASSED  
builtin_exit                      : PASSED  
builtin_glob                      : PASSED  
builtin_glob_archive              : Set environmental variable 'DO_DIFF' to examine the difference.  
FAILED  
builtin_split_by_characters       : PASSED  
bzip2                             : PASSED  
c_file                            : PASSED  
chain                             : PASSED  
clean                             : PASSED  
composite                         : PASSED  
conditionals                      : PASSED  
conditionals2                     : PASSED  
conditionals3                     : PASSED  
conditionals_multiple             : PASSED  
configuration                     : PASSED  
copy_time                         : PASSED  
core_action_output                : PASSED  
core_action_status                : PASSED  
core_actions_quietly              : PASSED  
core_at_file                      : PASSED  
core_bindrule                     : PASSED  
core_jamshell                     : PASSED  
core_multifile_actions            : PASSED  
core_nt_cmd_line                  : PASSED  
core_option_d2                    : PASSED  
core_option_l                     : PASSED  
core_option_n                     : PASSED  
core_parallel_actions             : PASSED  
core_parallel_multifile_actions_1 : PASSED  
core_parallel_multifile_actions_2 : PASSED  
core_source_line_tracking         : PASSED  
core_update_now                   : PASSED  
core_variables_in_actions         : PASSED  
custom_generator                  : PASSED  
default_build                     : PASSED  
default_features                  : PASSED  
dependency_property               : PASSED  
dependency_test                   : PASSED  
direct_request_test               : PASSED  
disambiguation                    : PASSED  
dll_path                          : PASSED  
double_loading                    : PASSED  
duplicate                         : PASSED  
example_libraries                 : PASSED  
example_make                      : PASSED  
exit_status                       : PASSED  
expansion                         : PASSED  
explicit                          : PASSED  
feature_cxxflags                  : PASSED  
free_features_request             : PASSED  
generator_selection               : PASSED  
generators_test                   : PASSED  
implicit_dependency               : PASSED  
indirect_conditional              : PASSED  
inherit_toolset                   : PASSED  
inherited_dependency              : PASSED  
inline                            : PASSED  
lib_source_property               : PASSED  
library_chain                     : PASSED  
library_property                  : PASSED  
link                              : PASSED  
load_order                        : PASSED  
loop                              : PASSED  
make_rule                         : PASSED  
message                           : PASSED  
ndebug                            : PASSED  
no_type                           : PASSED  
notfile                           : PASSED  
ordered_include                   : PASSED  
out_of_tree                       : PASSED  
path_features                     : PASSED  
prebuilt                          : PASSED  
print                             : PASSED  
project_dependencies              : PASSED  
project_glob                      : PASSED  
project_id                        : PASSED  
project_root_constants            : PASSED  
project_root_rule                 : PASSED  
project_test3                     : PASSED  
project_test4                     : PASSED  
property_expansion                : PASSED  
rebuilds                          : PASSED  
regression                        : PASSED  
relative_sources                  : PASSED  
remove_requirement                : PASSED  
rescan_header                     : PASSED  
resolution                        : PASSED  
scanner_causing_rebuilds          : PASSED  
searched_lib                      : PASSED  
skipping                          : PASSED  
sort_rule                         : PASSED  
source_locations                  : PASSED  
source_order                      : PASSED  
space_in_path                     : PASSED  
stage                             : PASSED  
standalone                        : PASSED  
static_and_shared_library         : PASSED  
suffix                            : PASSED  
tag                               : PASSED  
test_result_dumping               : PASSED  
test_rc                           : PASSED  
testing_support                   : PASSED  
timedata                          : PASSED  
toolset_requirements              : PASSED  
unit_test                         : PASSED  
unused                            : PASSED  
use_requirements                  : PASSED  
using                             : PASSED  
wrapper                           : PASSED  
wrong_project                     : PASSED  
zlib                              : PASSED  
symlink                           : PASSED  
  
        === Test summary ===  
        PASS: 127  
        FAIL: 2  
```  
  
I'll push an intermediate revision without the fix to demonstrate the broken behavior on Travis.

---

## Comment 5

> Username: vmrob  
> Created_at: 2016-10-09 02:25:48 UTC  
> Updated_at: 2016-10-10 20:06:31 UTC  
> Url: https://github.com/boostorg/build/pull/136#issuecomment-252460151  

The above [travis builds](https://travis-ci.org/boostorg/build/builds/166141860) appears to be failing as a result of the bug. Note the failure in `core_language`. As soon as you can confirm that the bug does appear to exist, I'll go ahead and force push the original version of this PR. Alternatively, the first commit, 74224eb918c690be944cf27bb2efd35852818200, is perfectly suitable for cherry-picking.

---

## Comment 6

> Username: vmrob  
> Created_at: 2016-11-04 23:32:13 UTC  
> Url: https://github.com/boostorg/build/pull/136#issuecomment-258572419  

I've rebased this patch on top of the latest develop. @vprus have you had any time to review the patch? See the inline comments on the commit for a more detailed breakdown.

---

## Review 7 [Commented]

> Username: vmrob  
> Created_at: 2016-11-04 23:39:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/build/pull/136#pullrequestreview-7300662  

Description of bug and steps to resolve it.

📁 src/engine/builtins.c

```diff
2508 |         {
2518 |-             if ( strip_eol_opt )
2519 |-                 rtrim( buffer );
```

> Username: vmrob  
> Created_at: 2016-11-04 23:33:16 UTC  
> Updated_at: 2016-11-04 23:39:43 UTC  
> Url: https://github.com/boostorg/build/pull/136#discussion_r86649453  

Previously, this would occur _inside_ the loop which splits output strings on 1024 byte boundaries.

---

📁 src/engine/builtins.c

```diff
2515 | 
2516 |+     if ( strip_eol_opt )
2517 |+         string_rtrim( &s );
```

> Username: vmrob  
> Created_at: 2016-11-04 23:33:36 UTC  
> Updated_at: 2016-11-04 23:39:05 UTC  
> Url: https://github.com/boostorg/build/pull/136#discussion_r86649477  

By moving it outside the loop and only processing the final string, the bug is mitigated.


📁 src/engine/strings.c

```diff
 245 |+         string_rtrim( bar_copy );
 246 |+         assert( !strcmp( bar_copy->value, "Bar" ) );
 247 |+     }
```

> Username: vmrob  
> Created_at: 2016-11-04 23:34:31 UTC  
> Updated_at: 2016-11-04 23:39:05 UTC  
> Url: https://github.com/boostorg/build/pull/136#discussion_r86649535  

I do not know how to run these tests. I'm reasonably confident they work, but I do not know how to verify this. **How can I verify that these tests function as expected?**


📁 test/core-language/test.jam

```diff
1514 |+ # buffered output
1515 |+ 
1516 |+ local expected = "When the shell output buffer splits on whitespace, the whitespace shouldn't be trimmed.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             end." ;
```

> Username: vmrob  
> Created_at: 2016-11-04 23:38:44 UTC  
> Updated_at: 2016-11-04 23:39:05 UTC  
> Url: https://github.com/boostorg/build/pull/136#discussion_r86649876  

This string is the key component to these tests. It's hard to see, but there is a large amount of whitespace between `trimmed.` and `end.`. The length of this string is 1096 bytes. Provided that the output is split in 1024 byte regions, the first chunk will have a large amount of whitespace trailing after `trimmed.`.  
  
The more common error case (as I've seen), is when trying to parse something like the output of a `pkg-config` command and the whitespace boundary just _happens_ to be between a set of cflags or linker flags.


---

## Comment 8

> Username: vmrob  
> Created_at: 2016-12-05 04:11:51 UTC  
> Updated_at: 2016-12-05 04:16:39 UTC  
> Url: https://github.com/boostorg/build/pull/136#issuecomment-264764250  

@vprus Have you had a chance to review this patch? Are there any other maintainers who I should be asking for? @grafikrobot, @frenchtoast747?

---

## Comment 9

> Username: vmrob  
> Created_at: 2017-01-31 08:40:18 UTC  
> Url: https://github.com/boostorg/build/pull/136#issuecomment-276305491  

@eldiener I noticed you are an active contributor to this project. Do you think you could take a look at this PR? Thanks!

---

## Comment 10

> Username: eldiener  
> Created_at: 2017-02-23 04:09:55 UTC  
> Url: https://github.com/boostorg/build/pull/136#issuecomment-281889286  

Testing out your PR I am seeing the core_language test failing, whereas it normally succeeds on the 'develop' branch. I using msvc-14.0 on Windows.

---

## Comment 11

> Username: vmrob  
> Created_at: 2017-02-24 00:18:43 UTC  
> Url: https://github.com/boostorg/build/pull/136#issuecomment-282164816  

@eldiener Just tested this patch rebased against develop (1949282) and core_language seems to work on Darwin... I don't have quick access to MSVC to test this with, but I'm betting there's something in the shell command that isn't transferring correctly.  
  
It relies on parsing the output of an echo command exceeding 1024 characters:  
  
```  
echo "When the shell output buffer splits on whitespace, the whitespace shouldn't be trimmed.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             end."  
```  
  
Any ideas?

---

## Comment 12

> Username: eldiener  
> Created_at: 2017-02-24 14:45:23 UTC  
> Url: https://github.com/boostorg/build/pull/136#issuecomment-282308448  

If you run  Windows you can get a free version of VS2015 with which to test. If you do not run Windows then of course the free version does you no good. I will try to test on Linux to see what my results are. I do not have a Mac.

---

## Comment 13

> Username: eldiener  
> Created_at: 2017-05-03 00:08:24 UTC  
> Url: https://github.com/boostorg/build/pull/136#issuecomment-298795205  

Sorry for the delay with this. I did test under Linux and the tests passed with your fix. I now have to try to figure out why the core_language test is failing with VC++ under Windows.

---

## Comment 14

> Username: swatanabe  
> Created_at: 2017-05-03 00:27:43 UTC  
> Url: https://github.com/boostorg/build/pull/136#issuecomment-298797560  

AMDG  
  
On 05/02/2017 06:08 PM, Edward Diener wrote:  
> Sorry for the delay with this. I did test under Linux and the tests passed with your fix. I now have to try to figure out why the core_language test is failing with VC++ under Windows.  
>   
  
  It fails because echo behaves differently on Windows  
(the quotes appear in the output).  
  
In Christ,  
Steven Watanabe

---

## Review 15 [Commented]

> Username: eldiener  
> Created_at: 2017-05-03 05:25:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/build/pull/136#pullrequestreview-35941377  

📁 test/core-language/test.jam

```diff
1516 |+ local expected = "When the shell output buffer splits on whitespace, the whitespace shouldn't be trimmed.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             end." ;
1517 |+ local buffered = "echo \"$(expected)\"" ;
1518 |+ if $(OS) = VMS { buffered = "PIPE WRITE SYS$OUTPUT \"$(expected)\"" ; }
```

> Username: eldiener  
> Created_at: 2017-05-03 05:25:01 UTC  
> Url: https://github.com/boostorg/build/pull/136#discussion_r114474090  

You need to add this line after 1518:  
  
`if $(OS) = NT { buffered = "echo $(expected)" ; }`  
  
That addition fixes the problem where your test does not pass under Windows.


---

## Comment 16

> Username: eldiener  
> Created_at: 2017-05-03 05:43:59 UTC  
> Url: https://github.com/boostorg/build/pull/136#issuecomment-298827508  

Thanks Steven. I suggested a fix for the PR that works in my testing.

---

## Comment 17

> Username: eldiener  
> Created_at: 2017-05-09 00:47:04 UTC  
> Url: https://github.com/boostorg/build/pull/136#issuecomment-300030051  

I have tested this fix on both Windows and Linux and it works. After merging I will update this fix with the fix I specified above for Windows and push it to develop.

---

## Comment 18

> Username: eldiener  
> Created_at: 2017-05-09 00:50:39 UTC  
> Url: https://github.com/boostorg/build/pull/136#issuecomment-300030501  

I am not able to merge this PR online. I do not know why but I suspect it may have conflicts with the base branch.

---

## Comment 19

> Username: eldiener  
> Created_at: 2017-05-09 03:05:49 UTC  
> Url: https://github.com/boostorg/build/pull/136#issuecomment-300048848  

I pushed this fix to develop on my own, along with my own fix for Windows.

---
