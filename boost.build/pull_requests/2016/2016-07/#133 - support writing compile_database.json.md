# #133 support writing compile_database.json [Closed]

> Username: arvidn  
> Created at: 2016-07-08 04:42:49 UTC  
> Updated at: 2021-10-02 22:08:11 UTC  
> Closed at: 2018-08-20 13:42:35 UTC  
> Url: https://github.com/boostorg/build/pull/133  

to be used with clang tooling

---

## Comment 1

> Username: jhunold  
> Created_at: 2016-07-08 15:59:54 UTC  
> Url: https://github.com/boostorg/build/pull/133#issuecomment-231399008  

That is a good idea. Unfortunately the usage of getcwd() is wrong. I've created https://github.com/arvidn/build/pull/1 with the fix.  
And it would be nice if the "-n" switch would print out the compile database, too. Then recreating the databases can be done by a simple "b2 -c -a -n" without recompiling everything.

---

## Comment 2

> Username: grafikrobot  
> Created_at: 2016-07-08 16:09:40 UTC  
> Url: https://github.com/boostorg/build/pull/133#issuecomment-231401458  

On Fri, Jul 8, 2016 at 10:59 AM, Jürgen Hunold notifications@github.com  
wrote:  
  
> That is a good idea. Unfortunately the usage of getcwd() is wrong. I've  
> created arvidn#1 https://github.com/arvidn/build/pull/1 with the fix.  
> And it would be nice if the "-n" switch would print out the compile  
> database, too. Then recreating the databases can be done by a simple "b2 -c  
> -a -n" without recompiling everything.  
  
Yes, please make it output the database with the -n option also. As it  
would also make it easier for me to evaluate the results (I don't fancy  
doing a long compile just to read what's in the db). For example I don't  
know how well this will work with tests style targets which remove some  
intermediate files without reading carefully through the db output. And  
also env var handling, and response files, etc.

---

## Comment 3

> Username: arvidn  
> Created_at: 2016-07-08 22:01:42 UTC  
> Url: https://github.com/boostorg/build/pull/133#issuecomment-231483439  

I fixed cwd() issue and made the test run with -n (which already worked). There are a few things I'm not super happy with.  
1. The json output has a trailing comma after the last compile command  
2. There is a heuristic that names of compile-actions contain the string "compile". This is to not include linking, creating directories etc.  
3. the json string escape function is pretty light-weight. It basically ignores non-ascii characters.

---

## Comment 4

> Username: frenchtoast747  
> Created_at: 2016-07-08 22:38:36 UTC  
> Url: https://github.com/boostorg/build/pull/133#issuecomment-231489362  

One concern that I have is that it seems too purposed for the database itself.  
  
```  
if '-c' in ARGV  
    dump_json()  
```  
  
Something I would like to see out of this would be to make it slightly more customizable: preferably a registry of for callback functions. Once an action is completed the registered callback functions would fire with the same input as currently provided. This is similar to what @vprus has done on the server branch. Having a callback system allows for dumping in JSON, XML, or maybe even allows for creating IDE specific project files. A step further would allow registering Jam or Python functions as callbacks rather than just C functions.  
  
Just some things I have been thinking about.

---

## Comment 5

> Username: vprus  
> Created_at: 2016-07-12 18:52:09 UTC  
> Url: https://github.com/boostorg/build/pull/133#issuecomment-232143409  

I was living in the cave recently, so missed this clang development.  
- Once I have compile_database.json, what can I do with it  
- Boost.Build can compile targets as part of configuration checks - how does that interact with compile_database.json?  
  
Thanks,

---

## Comment 6

> Username: arvidn  
> Created_at: 2016-07-12 21:06:17 UTC  
> Url: https://github.com/boostorg/build/pull/133#issuecomment-232181347  

The main tool (as far as I know, and that I'm using) is clang-tidy. It's a tool that leverages the clang AST and runs [checkers](http://clang.llvm.org/extra/clang-tidy/checks/list.html) on it. Checkers implement a fairly wide range of tools, such as:  
- static analysis  
- warnings that are more sophisticated than typical compiler warnings  
- code formatting/style enforcement  
- code transformations, like rewriting traditional for-loops as range-for loops or converting const-reference parameters into by-value arguments with std::move().  
  
It lowers the barrier to write additional tools for custom warnings/enforcement of style.  
  
My understanding is that the compile_database.json is only used by clang tools to lookup the command line invocation for a specific source file, in order to transform it to an AST. Specifically to use the same defines and include paths. Having additional source files show up in the compile data base should not cause any problems, unless a file is built twice.  
  
In fact, it occurs to be that the mapping here is not obvious, given that a single bjam invocation can build multiple configurations of the same file. With this patch, every configuration is included in the compile database, despite it really being meant to be used for a single configuration.  
  
As I have experimented with this patch and clang-tidy I've also been tempted to add a boost-build toolset for clang-tidy. Such toolset would probably have to generate a temporary compile_commands.json and point clang-tidy at it. I'm not very familiar with the details of toolsets and what one can do in them, but if it's possible to capture command line arguments, write them to a file in a temporary directory, I would expect _this_ patch to not be necessary.

---

## Comment 7

> Username: arvidn  
> Created_at: 2016-07-12 21:11:54 UTC  
> Url: https://github.com/boostorg/build/pull/133#issuecomment-232182825  

The reason I'm increasingly thinking that a toolset is the most reasonable integration is that clang-tidy itself is not very well suited to be run over multiple files at a time (despite the --help giving that impression). It does not apply code transformations until all checkers are run on all files, which means a header file that's included by more than one translation unit may get the same transformations applied multiple times. Running clang-tidy on one file at a time circumvents that problem (but would still require -j1).  
  
Invoking the tool on one source file at a time, typically, requires a fairly complex bash command line involving find and xargs. Visiting every file and invoking a command seems like a much more appropriate thing for the build system to do.

---

## Comment 8

> Username: tee3  
> Created_at: 2016-08-06 02:25:53 UTC  
> Url: https://github.com/boostorg/build/pull/133#issuecomment-238001129  

@arvidn Does running clang-tidy on individual files also require a compilation database?  The documentation seemed to suggest that it might.

---

## Comment 9

> Username: arvidn  
> Created_at: 2016-08-06 02:33:21 UTC  
> Url: https://github.com/boostorg/build/pull/133#issuecomment-238001427  

@tee3 as far as I can tell, yes. I believe the idea is that you first produce a compilation database and then run clang-tidy on individual files, using the same one. I'm thinking that it might make sense to make a toolset that constructs a temporary compilation database, similar to response files, right before invoking it. I must admit though that it's not obvious to me how that's done in msvc.jam

---

## Comment 10

> Username: tee3  
> Created_at: 2016-08-06 03:36:07 UTC  
> Url: https://github.com/boostorg/build/pull/133#issuecomment-238003500  

@arvidn I have some ideas, but haven't tried any of them.  I definitely agree that clang-tidy would be better run by the build system.  It allows the tool to be run with every configuration you build from Boost.Build, which might be necessary for sections that are disabled by the preprocessor in certain configurations.  
  
I was considering a more general "toolset wrapper" concept such that any tool could be run with the same properties as the wrapped toolset.  Something like this could also be used to update a compilation database or a source code indexer like `rtags`.  This would assume that the Boost.Build project was written at a high-enough level (using `<include>` instead of `<cflags>-Iinclude-dir`, etc.) as most are.  I don't have a clear idea if this would be possible. Maybe  @vprus or @swatanabe have an opinion on this or another idea?  
  
I was really hoping it would not require a compilation database.  However, I don't think that would be impossible to deal with.  A compilation database for a single file could be generated for a single source file (named appropriately) just before clang-tidy was run on the file and then referenced via the `-p` option to `clang-tidy`.  
  
I don't think you will need to do it with the msvc toolset, but by inheriting from the gcc toolset in a similar way that the clang toolset does.  I may do some experimentation at some point in the near future.  In the meantime, here are some thoughts.  
  
From reading the documentation, it seems to me that running clang-tidy in the `compile.c` and `compile.c++` actions with all options you want to give it would be the right way to go.  One could even write an "object" file (maybe containing a report) if clang-tidy returned success, so it would not have to be run again if the source file wasn't changed.  
  
I think the clang-tidy toolset would inherit from the gcc toolset and use something like the following within the `compile.c++` actions using the paths from `<include>` and the defines from `<define>`.  
  
**UNTESTED**  
  
```  
# in clang-tidy.jam  
actions compile.c++  
{  
  # this could use whatever properties you want, following clang.jam compiler actions  
  clang--tidy $(>) -- -D$(DEFINES) -I"$(INCLUDES)" > $(<)  
}  
```  
  
If a compilation database was required, then the following might be good enough for *nix, though not portable since echo is pretty different between *nix and Windows.  I'm sure there's a portable solution, but I haven't worked through it yet.  
  
**UNTESTED**  
  
```  
# in clang-tidy.jam  
actions compile.c++  
{  
  echo "" > $(<:S=.json)  
  echo "[" >> $(<:S=.json)  
  echo "{" >> $(<:S=.json)  
  echo "\"file\" : \"$(>)\"," >> $(<:S=.json)  
  echo "\"directory\" : \"$(>:D)\"," >> $(<:S=.json)  
  # this could use whatever properties you want, following clang.jam compiler actions  
  echo "\"command\" : \"clang++ -D$(DEFINES) -I\"$(INCLUDES)\"\"" >> $(<:S=.json)  
  echo "}" >> $(<:S=.json)  
  echo "]" >> $(<:S=.json)  
  
  # this could use whatever properties you want, following clang.jam compiler actions  
  clang--tidy -p$(<:S=.json) $(>) -- -D$(DEFINES) -I"$(INCLUDES)" > $(<)  
}  
```  
  
Unfortunately, toolsets do more than compile.  
- `exe` target requires the `link` action  
- `lib` target requires the `archive` action  
- etc.  
  
I suppose one way would be to just assume success and write a file if you get to the link action.  There may be a better way to deal with this.

---

## Comment 11

> Username: arvidn  
> Created_at: 2016-10-24 00:22:32 UTC  
> Url: https://github.com/boostorg/build/pull/133#issuecomment-255625218  

despite this not being the best way to run clang-tidy, I still think there's value in being able to produce a compile database, for other tools. My impression is that it's increasingly becoming the standard way of exporting build commands for tools.

---

## Comment 12

> Username: hia3  
> Created_at: 2017-01-31 15:46:23 UTC  
> Url: https://github.com/boostorg/build/pull/133#issuecomment-276400314  

I can't believe that people from clang invented such a thing as `compile_database.json`. It could only work in a language without preprocessor. There should be multiple `compile_database.json` files (as there are multiple `*.rsp` files generated by BB), some of you guys should go and explain this to them in proper English.  
  
I've tried this pull request on Windows. Here is what I've got:  
  
[  
{ "directory": "D:\\my_dir\\jam", "command": "call \"C:\\Program Files (x86)\\Microsoft Visual Studio 14.0\\VC\\vcvarsall.bat\" x86_amd64 > nul     \"C:\\Program Files\\LLVM\\bin\\clang-cl.exe\" @\"D:\\my_dir\\compiled\\root_project\\bin.v2\\build\\clang-vc14-win\\release\\address-model-64\\link-static\\runtime-link-static\\user-interface-gui\\main.obj.rsp\" ", "file": "D:\\my_dir\\src\\main.cpp" },  
]  
  
Clang tools won't be able to understand what `vcvarsall.bat` does and how to read `main.obj.rsp`.  
Contents of `INCLUDE` environment variable should be included as "-I" option.

---

## Comment 13

> Username: arvidn  
> Created_at: 2018-08-20 13:42:35 UTC  
> Url: https://github.com/boostorg/build/pull/133#issuecomment-414320693  

obsoleted by https://github.com/boostorg/build/pull/334

---
