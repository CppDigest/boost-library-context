# #511 Fix: corrected empty double quotes being added to cmd.exe /c on Windo… [Merged]

> Username: fkonvick  
> Created at: 2025-08-22 11:35:14 UTC  
> Updated at: 2025-10-21 06:46:51 UTC  
> Merged at: 2025-10-21 06:46:50 UTC  
> Closed at: 2025-10-21 06:46:51 UTC  
> Url: https://github.com/boostorg/process/pull/511  

We used to run `bp::child(bp::shell, bp::args = {shell_script}, ...` which resulted in `C:\Windows\system32\cmd.exe /c shell_script` out of `build_args`.  
  
After #256 we're getting `C:\Windows\system32\cmd.exe /c "" shell_script` (note the extra empty pair of double quotes) and the script refuses to run.  
  
This code in `exe_args_shell` with Process v1 on Windows:  
  
```  
std::vector<string_type> args_ = {c_arg(Char()), std::move(exe)};  
args_.insert(args_.end(), std::make_move_iterator(args.begin()), std::make_move_iterator(args.end()));  
```  
  
adds `exe` to `args_`, but this might be empty, especially with `bp::shell`, and (newly after #256) gets translated to unwanted `""` rather than being omitted, which causes prior user code to break.  
  
This PR only adds `exe` to `args_` if non-empty, so it fixes the use case described above.  
  
Also see my comments in #256 and related #73 for further info.  
  
Using `bp::exe = {shell_script}` instead of `bp::args` might be a workaround for some.

---
