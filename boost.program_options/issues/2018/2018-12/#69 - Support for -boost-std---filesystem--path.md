# #69 - Support for <boost/std>::filesystem::path [Open]

> Username: Flamefire  
> Created at: 2018-12-09 18:04:02 UTC  
> Updated at: 2022-08-24 16:03:55 UTC  
> Url: https://github.com/boostorg/program_options/issues/69  

Basically the same issue from the mailing list which is open since Boost 1.35: http://boost.2283326.n4.nabble.com/program-options-Problem-with-paths-that-have-spaces-td2576490.html  
  
Problem: You cannot take a `*::filesystem::path` param that contains spaces.  
  
Reason: BPO uses `lexical_casts` which uses stream extraction stopping on first whitespace  
  
Possible solution: Special case for types having a ctor accepting a string and using that.  
  
Related issue: https://github.com/boostorg/lexical_cast/issues/25 but won't be fixed there. Consider: `lexical_cast<path>("\"/home/my user\"")` is considered OK but calling a program with `./foobar "\"/home/my user\""` is not OK. So this is a BPO issue

---

## Comment 1

> Username: Flamefire  
> Created at: 2020-01-01 15:33:11 UTC  
> Url: https://github.com/boostorg/program_options/issues/69#issuecomment-570061985  

This won't be fixed by https://github.com/boostorg/lexical_cast so it needs to be handled here.

---

## Comment 2

> Username: UMU618  
> Created at: 2020-09-09 11:01:25 UTC  
> Url: https://github.com/boostorg/program_options/issues/69#issuecomment-689488250  

I don't think it's a bug. It depends on your shell.  
  
try calling a program with:  
  
```sh  
./foobar "\"/home/my user\""  
./foobar "\""/home/my user\"""  
./foobar '\"/home/my user\"'  
```  
  
I test on Windows, in cmd, 1st is OK, and in powershell, 2nd and 3rd are OK.

---

## Comment 3

> Username: Flamefire  
> Created at: 2020-09-09 11:51:03 UTC  
> Url: https://github.com/boostorg/program_options/issues/69#issuecomment-689510723  

I think this is missing my point. Usually a program is called as `./foobar "/home/my user"` not with several escapes and the like. And when you use BPO and want to take a path you'd add a `po::value<fs::path>` and expect the above invocation to work. Requiring the invoker of the program to play escaping games isn't feasible.

---

## Comment 4

> Username: UMU618  
> Created at: 2020-09-10 04:06:13 UTC  
> Url: https://github.com/boostorg/program_options/issues/69#issuecomment-689965941  

If you want `./foobar "/home/my user"` working, you need to add a `po::value<std::string>`.  
  
If you add a `po::value<fs::path>`, then you escape the path. (Some shell may change the arguments.)

---

## Comment 5

> Username: Flamefire  
> Created at: 2020-09-10 07:50:34 UTC  
> Url: https://github.com/boostorg/program_options/issues/69#issuecomment-690058834  

Yes that is the summary of how BPO currently works. But I'm arguing that this is suboptimal.  
  
Ok, let's start with a step backwards: Suppose you want to write an application that takes a filepath (e.g. image file) and some other parameter (say a size). So CLI is: `./foo <path> <size>`. As with literally every other CLI the user has to make sure their shell passes `<path>` as a single argument. So the following are fine:  
  
- ./foo img.gif 10  
- ./foo "img.gif" 10  
- ./foo folder/img.gif 10  
- ./foo "folder/img.gif" 10  
- ./foo folder\ space/img.gif 10  
- ./foo "folder space/img.gif" 10  
  
When you are not using BPO your code would look like this:  
```  
int main(char** argv, int argc){  
  if(argc < 3) return 1;  
  fs::path image = argv[1];  
  int size = strtoint(argv[2]);  
}  
```  
  
Translated to BPO this *should* look like (Ignoring that I'm not using positional options for simplicity):  
```  
int main(char** argv, int argc){  
  fs::path image;  
  int size;  
  po::options_description desc("Allowed options");  
  desc.add_options()("i", po::value<fs::path>(&image), "<img>")("s", po::value<int>(&size), "<img>");   
...  
}  
```  
  
This now works for all cases but the last 2. And changing the the CLI requiring the user to pass quotes is bogus. How would you explain they need to do `./foo "\"folder space/img.gif\"" 10` do use a path with spaces which is wrong for every other CLI program.  
  
The point here is: The correctly escaped path is already passed as a single argument to the program. It can be retrieved via `argv[1]`. Quoting is only required by the user to get that single path into a single argument. He doesn't need to quote the quotes.  
  
Now what is actually required by BPO is:  
```  
int main(char** argv, int argc){  
  std::string imageAsStr;  
  int size;  
  po::options_description desc("Allowed options");  
  desc.add_options()("i", po::value<std::string>(&imageAsStr), "<img>")("s", po::value<int>(&size), "<img>");   
...  
  fs::path image = imageAsStr;  
}  
```  
  
This is inconvenient at best. And what is even worse: The first, straight forward approach works very well until someone passes a path with spaces where it silently(?) breaks.  
  
So I really don't agree with:  
> If you add a po::value<fs::path>, then you escape the path. (Some shell may change the arguments.)  
  
It would be the user who needs to add (quoted) quotes although he wouldn't know when and why. And it's not about the shell "changing arguments". (What do you mean by that? That for `./foo folder space/img.gif 10` you'd get 3 arguments instead of 2? That is expected and common to all CLI programs)  
  
So my solution would be: If a value is constructible from a string or `const char*` then that should be used instead of a lexical_cast. Because the ctor can be expected to consume the whole string which lexical_cast does not.

---

## Comment 6

> Username: sandercox  
> Created at: 2021-02-08 22:51:13 UTC  
> Url: https://github.com/boostorg/program_options/issues/69#issuecomment-775516508  

For those coming here and wanting a working option what I just did add the following to the file doing the parsing. Now the lexical_cast that is used will be specialized with std::filesystem::path and just call the constructor like discussed here. Even if it's not the official method, this might be a work around for others.  
  
```c++  
namespace boost  
{  
    template <>  
    inline std::filesystem::path lexical_cast<std::filesystem::path, std::basic_string<char>>(const std::basic_string<char> &arg)  
    {  
        return std::filesystem::path(arg);  
    }  
}  
```

---

## Comment 7

> Username: Noxitu  
> Created at: 2022-08-24 15:46:03 UTC  
> Updated at: 2022-08-24 16:03:55 UTC  
> Url: https://github.com/boostorg/program_options/issues/69#issuecomment-1225904699  

Just discovered this behavior and I also would suggest fixing it.  
  
The defense of current behavior is that it makes sense from "keep it simple" perspective. But the fact is that `operator>>`, and thus `lexical_cast` (despite its name) is not the correct way to parse `string` into `std::filesystem::path`.   
  
I think **sandercox** solution is easy and would work for the simple use cases, but is a wrong choice - as it can end up with multiple definitions for same symbol - meaning UB.  
  
I think one way of fixing it while keeping backward compatibility would be introducing `po::path_value` or some generic replacement for string constructible objects that is capable of supporting this conversion. Although even in such case I would argue that `po::value<std::filesystem::path>` should produce a compilation warning (maybe via `[[deprecated]]`?).
