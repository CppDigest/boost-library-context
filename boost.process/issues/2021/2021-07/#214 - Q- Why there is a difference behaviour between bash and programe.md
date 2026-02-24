# #214 - Q: Why there is a difference behaviour between bash and programe? [Closed]

> Username: cathaysia  
> Created at: 2021-07-20 10:35:44 UTC  
> Updated at: 2022-06-11 06:41:40 UTC  
> Closed at: 2022-06-11 06:41:40 UTC  
> Url: https://github.com/boostorg/process/issues/214  

Hi, friends, I'm sorry for asking question here, please forgive what mistake I do. :( If you dislike this, please close it.  
  
I'm try to run a bash script and read it's stdout. These script  can run well at a shell, but it goes error when it in code.  
  
What code I make is:  
```C++  
std::string DotFile::runBash(const std::string& cmd) {  
    bp::ipstream is;  
    auto cmdString = fmt::format(R"RRR(bash -c 'echo `env LANG=en_US.UTF-8 {}`')RRR", cmd);  
    spdlog::debug(fmt::format("the command is： {}", cmdString));  
  
    bp::child c(cmdString, bp::std_out > is);  
    std::vector<std::string> data;  
    std::string line;  
    while(c.running() && std::getline(is, line) && !line.empty()) data.push_back(line);  
    auto result = boost::join(data, "\n");  
    spdlog::debug("result of bash is： {}", result);  
    return result;  
}  
```  
  
Here is it's log :  
  
```log  
debug: the command is： bash -c 'echo `env LANG=en_US.UTF-8 getfacl /a/3 | egrep -v "(file:)|(owner: )|(group: )|(mask)" | tr "\n" ";" | sed "s/;;/\n/g" | sed "s/# //g"`'  
`env: -c: line 1: unexpected EOF while looking for matching `''  
`env: -c: line 2: syntax error: unexpected end of file  
debug: result of bash is：  
```  
  
That is, the shell script is   
  
```bash  
bash -c 'echo `env LANG=en_US.UTF-8 getfacl /a/3 | egrep -v "(file:)|(owner: )|(group: )|(mask)" | tr "\n" ";" | sed "s/;;/\n/g" | sed "s/# //g"`'  
```  
  
I can't understand why it goes wrong :(

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2022-06-11 06:41:40 UTC  
> Url: https://github.com/boostorg/process/issues/214#issuecomment-1152868279  

That's because the whole idea of cmd-style was misguided. You should use the exe-args style:  
  
```cpp  
bp::child c("/bin/bash", bp::args={"-c", format("env LANG=en_US.UTF-8 {}", cmd)}, bp::std_out > is);  
```
