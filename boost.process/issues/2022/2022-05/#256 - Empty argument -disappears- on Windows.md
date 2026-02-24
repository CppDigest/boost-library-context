# #256 - Empty argument "disappears" on Windows [Closed]

> Username: Capital-Z  
> Created at: 2022-05-31 01:35:01 UTC  
> Updated at: 2025-08-22 12:02:21 UTC  
> Closed at: 2023-08-14 09:47:41 UTC  
> Url: https://github.com/boostorg/process/issues/256  

Passing empty argument to a process just adds an extra space to its command line on Windows which is normally ignored. Not sure how it would work on Posix, but makes it impossible to pass empty value under Windows.  
Possible solution would be in ...process\detail\windows\basic_cmd.hpp build_args(..) which quotes arguments if necessary, to also quote empty ones. E.g. something like:  
`if(!arg.empty())  
{...}  
else  
arg = "\"\"";`

---

## Comment 1

> Username: SilverPlate3  
> Created at: 2023-07-03 05:09:08 UTC  
> Updated at: 2023-07-03 05:12:29 UTC  
> Url: https://github.com/boostorg/process/issues/256#issuecomment-1617340930  

@Capital-Z  
Can you please:  
1) post the code you tried to run.  
For example:  
```  
int main()  
{  
     boost::process::child("doesnt-exist");  
     child.wait()  
}  
```  
2) Elaborate on the desired outcome.  
  
I wasn't able to fully understand the issue.  
Thanks!

---

## Comment 2

> Username: Capital-Z  
> Created at: 2023-07-03 05:29:52 UTC  
> Url: https://github.com/boostorg/process/issues/256#issuecomment-1617382917  

If I remember correctly the original problem was with a custom executable and I solved it by passing data to it in other means. But the issue is simple: how to pass empty argument?  
As example ImageMagic has option -format and if you want to suppress it you pass empty string for format, i.e. from terminal: `magic.exe -format ""`  
So in boost process terms it would be (not verified code just typing here):  
`boost::process::child("magic.exe", boost::process::args({"-format", ""}));`  
According to my original comment this would pass just "-format" to magic.exe, which is not valid input for it.  
I am not sure if my original digging in boost/process code is still relevant.

---

## Comment 3

> Username: SilverPlate3  
> Created at: 2023-07-05 17:17:15 UTC  
> Updated at: 2023-07-05 17:17:47 UTC  
> Url: https://github.com/boostorg/process/issues/256#issuecomment-1622172339  

@klemens-morgenstern   
Fixed the issue in pull request https://github.com/boostorg/process/pull/328  
I think issue can be closed.

---

## Comment 4

> Username: fkonvick  
> Created at: 2025-08-22 08:55:02 UTC  
> Url: https://github.com/boostorg/process/issues/256#issuecomment-3213611480  

@klemens-morgenstern @SilverPlate3 This broke existing code for us.  We used to:  
`bp::child(bp::shell, bp::args = {shell_script}, ...`  
which resulted in `C:\Windows\system32\cmd.exe /c shell_script` out of `build_args` or, in case there was a space or `"` in the shell script, a quoted/escaped version: `C:\Windows\system32\cmd.exe /c "shell_script"`.  See PR #73 for fixes that were earlier introduced to fix escaping.  
  
With this change in PR @256, we're getting  
`C:\Windows\system32\cmd.exe /c "" shell_script` (note the extra empty pair of double quotes) and the script refuses to run.  
  
I see that the co-culprit is `exe_args_shell`:  this code  
```  
std::vector<string_type> args_ = {c_arg(Char()), std::move(exe)};  
args_.insert(args_.end(), std::make_move_iterator(args.begin()), std::make_move_iterator(args.end()));  
```  
adds `exe` to `args_`, but this might be empty, especially with `bp::shell`, and (newly) gets translated to the unwanted `""`.  Since this is added to args internally by BP, the end user has no visibility/clue of where this `""` is coming from.  Using `bp::exe = {shell_script}` instead of `bp::args` is a workaround that I can use now, but I still think that `exe_args_shell` should only add `exe` when it's non-empty.  Something like  
```  
std::vector<string_type> args_ = {c_arg(Char())};  
if ( !exe.empty() )  
  args_.emplace_back(std::move(exe));  
args_.insert(args_.end(), std::make_move_iterator(args.begin()), std::make_move_iterator(args.end()));  
```  
If you agree, I can make another PR or you could just go ahead and apply this :D

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2025-08-22 09:12:23 UTC  
> Url: https://github.com/boostorg/process/issues/256#issuecomment-3213668609  

A PR would be most welcome.

---

## Comment 6

> Username: fkonvick  
> Created at: 2025-08-22 12:02:21 UTC  
> Url: https://github.com/boostorg/process/issues/256#issuecomment-3214127852  

No problem, please see #511
