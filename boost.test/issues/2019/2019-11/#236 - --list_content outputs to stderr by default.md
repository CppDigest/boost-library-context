# #236 - --list_content outputs to stderr by default [Open]

> Username: K-ballo  
> Created at: 2019-11-20 14:51:13 UTC  
> Updated at: 2019-11-20 14:51:13 UTC  
> Url: https://github.com/boostorg/test/issues/236  

`--list_content`, as well as other commands (`--list_cases`, `--help`, etc) write their output to `stderr` by default. They should write their output to `stdout` instead.
