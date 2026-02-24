# #176 - boost::this_process::native_environment gets empty env [Open]

> Username: dirkarnez  
> Created at: 2020-07-22 10:36:10 UTC  
> Updated at: 2024-05-06 22:20:13 UTC  
> Url: https://github.com/boostorg/process/issues/176  

![image](https://user-images.githubusercontent.com/26408605/88166307-a4ee9700-cc49-11ea-9f9c-39db72c4c935.png)  
  
![image](https://user-images.githubusercontent.com/26408605/88166505-ef701380-cc49-11ea-99a7-72e157272a80.png)  
  
![image](https://user-images.githubusercontent.com/26408605/88166577-0d3d7880-cc4a-11ea-8118-3e146537dfd9.png)  
  
boost::this_process::native_environment on Linux returns non-empty env, however in Windows returns an empty env. Does it have nothing to do with boost? Thanks!

---

## Comment 1

> Username: lbarbieri  
> Created at: 2024-05-06 20:57:30 UTC  
> Url: https://github.com/boostorg/process/issues/176#issuecomment-2096903554  

I'm seeing a similar problem with version 1_81_0.  
  
For my case, it looks like the environment map lookup is case sensitive, but storage (at least on Windows) is case insensitive (as it should be).  
  
On the Windows system where I'm seeing a problem:  
1. `Path` is what's set in the native environment, and it's populated as expected.  
1. If I lookup `env["PATH"]` I get an empty entry.  
1. If I set `env["PATH"]` I blow-away the original content of `Path`.  
  
Printing the environment to `stdout` showed me the light:  
```c++  
auto &env = boost::this_process::environment();  
std::cout << "ENVIRONMENT:\n";  
for (auto i = env.begin() ; i != env.end() ; i++)  
{  
    std::cout << "  " << i->get_name() << " = " << i->to_string() << std::endl;  
}  
```

---

## Comment 2

> Username: lbarbieri  
> Created at: 2024-05-06 22:20:12 UTC  
> Url: https://github.com/boostorg/process/issues/176#issuecomment-2097022574  

Also can't get `append()` or `+=` to work reliably on Windows.  It either does nothing (`var.to_vector().size()` remains unchanged), or mangles the variable value, e.g. goes from size of 59 to 4, with what looks like a truncated value.
