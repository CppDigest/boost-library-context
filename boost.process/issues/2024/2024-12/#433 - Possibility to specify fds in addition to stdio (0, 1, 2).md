# #433 - Possibility to specify fds in addition to stdio (0, 1, 2) [Closed]

> Username: mika-fischer  
> Created at: 2024-12-02 16:42:52 UTC  
> Updated at: 2024-12-04 14:48:10 UTC  
> Closed at: 2024-12-04 14:48:08 UTC  
> Url: https://github.com/boostorg/process/issues/433  

It seems that currently, there's no way to pass fds to the child process through the boost process api, except for stdin, stdout, stderr. There's bind_fd, but it's posix only.  
  
I would like to create two pipe pairs and pass the readable end of the first to the child process as fd 3, and writable end of the second as fd 4, so that the child can then just read on fd3 and write to fd 4 to communicate with the parent.  
  
This is useful in case stdout/stderr is not usable for this purpose.  
  
Or am I missing something and this is already possible?

---

## Comment 1

> Username: mika-fischer  
> Created at: 2024-12-02 21:16:23 UTC  
> Url: https://github.com/boostorg/process/issues/433#issuecomment-2512868389  

Well, this seems much harder than I expected on Windows... In case you're considering this anyway, here's how libuv does it. No idea where they got the info about the reseved fields from...  
  
https://github.com/libuv/libuv/commit/3ec9c67f935adfd3515acaa1c5ec5222fbdc6f94

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2024-12-03 00:22:56 UTC  
> Url: https://github.com/boostorg/process/issues/433#issuecomment-2513251640  

That's because FDs aren't really a thing on windows, they're part of the C runtime. What you're inheriting on windows are HANDLEs.   
  
At the moment you're free to just do that by setting the [bInheritHandle](https://learn.microsoft.com/en-us/windows/win32/sysinfo/handle-inheritance) or using `SetHandleInformation`:  
  
```cpp  
::SetHandleInformation(my_handle, HANDLE_FLAG_INHERIT, TRUE);  
```  
  
NOTE: I do plan to [add a HANDLE whitelist](https://github.com/boostorg/process/pull/423) for windows (on v2) as well in 1.88 - at which point you'll need to add some initialization that's yet to be defined.  
  
I can't emulate FD mapping, because I would need to somehow inject code into the child process. The way I would recommend you go about this is to just use an environment variable and set the HANDLE values in there. (e.g. `MY_PIPE_HANDLES=0xDEADBEEF;0xBEEFDEAD`).   
  
If you need a FD for your api, you can create it using _open_osfhandle then. If you use something like asio, you would just directly use the HANDLE.

---

## Comment 3

> Username: mika-fischer  
> Created at: 2024-12-03 08:21:47 UTC  
> Url: https://github.com/boostorg/process/issues/433#issuecomment-2513838870  

I see, thanks for the suggestion.  
  
> I can't emulate FD mapping, because I would need to somehow inject code into the child process.  
  
Do note though that this is indeed possible without touching the child process, at least if the other process uses the CRT. See the commit posted above. You can use the same reserved fields in the STARTUPINFO that the CRT uses and set all fds from 0 to N to whatever handles you want. And the CRT on the other side will create the fds at startup.  
  
But yeah, it's quite a hack to be relying on those undocumented fields...

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2024-12-04 14:06:47 UTC  
> Url: https://github.com/boostorg/process/issues/433#issuecomment-2517512152  

Ok, so I took a looks at the libuv code, and this should work for you already on v2 (didn't compile it).   
But you'll probably need to add those to a whitelist starting in 1.88.  
  
  
```cpp  
template<std::size_t N>  
struct more_fds  
{  
  std::array<int, N> fds;  
  
  more_fds(std::array<int, N - 3> more)   
  {  
      fds[0] = 0;  
      fds[1] = 1;  
      fds[2] = 2;  
      std::copy(more.begin(), more.end(), fds.begin() + 3);  
  }  
  
  error_code on_setup(windows::default_launcher & launcher,   
                      const filesystem::path &,   
                      const std::wstring &) const  
  {  
  
    launcher.startup_info.StartupInfo.cbReserved2 = static_cast<WORD>(N * sizeof(int)); // I hope I read this right.  
    launcher.startup_info.StartupInfo.lpReserved2 = static_cast<BYTE*>(fds.data());  
    return error_code {};  
  };  
};  
```  
  
I think this is simple enough for users to add.

---

## Comment 5

> Username: mika-fischer  
> Created at: 2024-12-04 14:27:03 UTC  
> Url: https://github.com/boostorg/process/issues/433#issuecomment-2517596791  

No that's not quite correct. The format of the buffer is:  
  
- an `int` field (num_fds)  
- num_fds `unsigned char` fields (flags for each fd)  
- num_fds `uintptr_t` fields (`HANDLE` for each fd or `INVALID_HANDLE`)  
  
Flags can be seen [here](https://github.com/libuv/libuv/blob/3ec9c67f935adfd3515acaa1c5ec5222fbdc6f94/src/win/process.c#L37-L44)  
  
I'm also not sure what needs to be passed for 0-2, so they stay untouched.  
  
Still, it could be made to work using this approach, I guess.  
  
But all in all the approach to pass file descriptors or HANDLEs via env variables is simple enough and seems a lot less hacky than this. When I opened the issue, I thought this would be simple, but the more I research about this, the clearer it becomes what a mess this whole area is 😆   
  
So feel free to close. It's here for reference, in case anybody else feels the need to implement this.

---

## Comment 6

> Username: klemens-morgenstern  
> Created at: 2024-12-04 14:48:08 UTC  
> Url: https://github.com/boostorg/process/issues/433#issuecomment-2517650656  

Haha, even more reason not to implement this in a generic library.   
If you ever do and can share the code, I'd like to add it as an example for custom initializers.
