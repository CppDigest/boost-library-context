# #17 - build directory appears to be useless [Closed]

> Username: grisumbras  
> Created at: 2026-01-16 08:52:58 UTC  
> Updated at: 2026-01-19 10:18:11 UTC  
> Closed at: 2026-01-19 10:18:11 UTC  
> Url: https://github.com/boostorg/function_types/issues/17  

`build` subdirectory of this project is an exception to the convention that header-only libraries do not have that. This forces to check for this exception in some parts of Boost's build scripts e.g. (https://github.com/boostorg/boost/blob/master/Jamroot#L264-L266).  
  
I wanted to move these maintenance rules to `build.jam` and eliminate the exception. But it turns out the rules depend on Wave, which would result in this project depending on Wave, which is obviously bad.  
  
But then I tried building the targets, which failed, so I looked closely at the rules.  
  
https://github.com/boostorg/function_types/blob/6fba35ace59e9e7b1c3007ca6246a42ed6ead145/build/Jamfile#L14  
  
`../../..tools/wave` is not a thing. I guess, it was a thing in 2009. The actual (relative) location of the tool today seems to be `../../wave/tool/build`.  
  
https://github.com/boostorg/function_types/blob/6fba35ace59e9e7b1c3007ca6246a42ed6ead145/build/Jamfile#L11  
  
Uses source 1 as the program, and source 2 as the preprocessed file. But Target definitions instead pass the file to preprocess as the first argument, and the preprocessor as the second.  
  
https://github.com/boostorg/function_types/blob/6fba35ace59e9e7b1c3007ca6246a42ed6ead145/build/Jamfile#L17  
  
Targets are put into `build/timestamps` _subdirectory of the `build` project_. So, the rules do not update `build/timestamps/whatever`, they create `build/build/timestamps/whatever`.  
  
https://github.com/boostorg/function_types/blob/6fba35ace59e9e7b1c3007ca6246a42ed6ead145/build/Jamfile#L11  
That `-S../../..` thing adds an include directory. That's not an include directory of FunctionTypes, and even if it was, that's not enough, because FunctionTypes has dependencies.  
  
Given all that, `build/Jamfile` has no chance of performing its function. My intuition is that nobody has used it in at least 17 years (since the commit that introduced most of the stuff mentioned above). So, I suggest the entire directory is removed.

---

## Comment 1

> Username: pdimov  
> Created at: 2026-01-16 18:00:02 UTC  
> Url: https://github.com/boostorg/function_types/issues/17#issuecomment-3761184999  

Can you please submit a PR that deletes the `build/` directory and closes this issue?

---

## Comment 2

> Username: grisumbras  
> Created at: 2026-01-16 18:37:40 UTC  
> Url: https://github.com/boostorg/function_types/issues/17#issuecomment-3761309980  

Sure, I'll do it tomorrow.
