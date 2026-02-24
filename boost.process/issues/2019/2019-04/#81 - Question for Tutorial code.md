# #81 - Question for Tutorial code [Closed]

> Username: tanxn5  
> Created at: 2019-04-27 03:21:27 UTC  
> Updated at: 2023-08-02 05:02:28 UTC  
> Closed at: 2019-04-27 06:22:30 UTC  
> Url: https://github.com/boostorg/process/issues/81  

```  
std::vector<std::string> read_outline(std::string & file)  
{  
    bp::ipstream is; //reading pipe-stream  
    bp::child c(bp::search_path("nm"), file, bp::std_out > is);  
  
    std::vector<std::string> data;  
    std::string line;  
  
    while (c.running() && std::getline(is, line) && !line.empty())  
        data.push_back(line);  
  
    c.wait();  
  
    return data;  
}  
```  
  
What if  nm exit after  ```c.runing()``` return true?

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2019-04-27 03:29:51 UTC  
> Url: https://github.com/boostorg/process/issues/81#issuecomment-487250866  

Then the loop condition isn't fulfilled and child.wait reaps the zombie process from the table.

---

## Comment 2

> Username: tanxn5  
> Created at: 2019-04-27 03:41:00 UTC  
> Updated at: 2019-04-27 03:46:03 UTC  
> Url: https://github.com/boostorg/process/issues/81#issuecomment-487251401  

Thanks. maybe i misunderstood something, this routine:  
  
1. c.runing() = true  
2. (nm exited...)  
3. std::getline(is, line)    ---> is it safe?  
  
because there is a tutorial warning   
  
```  
Warning  
--  
The pipe will cause a deadlock if you try to read after nm exited  
```

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2019-04-27 06:22:30 UTC  
> Url: https://github.com/boostorg/process/issues/81#issuecomment-487259572  

The warning shouldn't be there (anymore). The pipe-sink is closed on the parent side, so there won't be any deadlock.  
  
Nm also writes out an empty line at the end, so it would be fine otherwise.

---

## Comment 4

> Username: tanxn5  
> Created at: 2019-04-28 01:37:16 UTC  
> Url: https://github.com/boostorg/process/issues/81#issuecomment-487332680  

Thanks.

---

## Comment 5

> Username: vt-alt  
> Created at: 2023-08-02 05:02:28 UTC  
> Url: https://github.com/boostorg/process/issues/81#issuecomment-1661499648  

@klemens-morgenstern But aren't FAQ is incorrect too?  
```  
[section:closep Why does the pipe not close?]  
  
Now for another example, which might look correct, let's consider you want  
to use `ls` to read the current directory.  
  
ipstream is;  
child c("ls", std_out > is);  
  
std::string file;  
while (is >> file)  
    cout << "File: " << file << endl;  
  
  
This will also deadlock, because the pipe does not close when the subprocess exits.  
So the `ipstream` will still look for data even though the process has ended.  
  
[note It is not possible to use automatic pipe-closing in this library, because  
a pipe might be a file-handle (as for async pipes on windows).]  
```  
The example code does not deadlock anymore too.
