# #201 - Cannot run process under Windows if working dir. contains parenthesis [Open]

> Username: ldionisi-nextflow  
> Created at: 2021-03-10 08:14:02 UTC  
> Updated at: 2021-03-10 08:14:29 UTC  
> Url: https://github.com/boostorg/process/issues/201  

The following code creates a script in a sub directory, executes it and redirect outputs to files. It works except if directory contains parenthesis:  
``` c++  
    const auto scriptFile = workspace / "testScript.bat";  
    std::ofstream ofs(scriptFile.string());  
    ofs << "echo success" << std::endl;  
    ofs.close();  
  
    const auto outputFile = workspace / "testScriptOut.txt";  
    const auto errFile = workspace / "testScriptErr.txt";  
  
    boost::process::child process(boost::process::detail::windows::build_args(scriptFile.string(), {}),  
                                  boost::process::std_out > outputFile,  
                                  boost::process::std_err > errFile,  
                                  ::boost::process::windows::hide);  
```  
It works in most of cases:  
- _dirWithoutSpace_: ok   
- _dir with space_ : ok  
- _dir(withParen)_ : ko. Error is "'D:\data\Computations\dir' is not recognized as an internal or external command, operable program or batch file."  
- _dir WithSpace(andParen)_ : this case is ok! mixing space and parenthesis is well handled  
- _dirWithé_ : ok  
  
I've tried different combinations with no success.  
  
The only way I found to make it works is to set the current path:  
``` c++  
    boost::filesystem::current_path(workspace);  
    boost::process::child process(boost::process::detail::windows::build_args("testScript.bat", {}),  
                                  boost::process::std_out > outputFile,  
                                  boost::process::std_err > errFile,  
                                  ::boost::process::windows::hide);  
  
```  
Thanks
