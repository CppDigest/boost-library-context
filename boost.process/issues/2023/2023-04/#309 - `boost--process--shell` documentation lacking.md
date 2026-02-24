# #309 - `boost::process::shell` documentation lacking [Closed]

> Username: HunterZ  
> Created at: 2023-04-24 06:35:00 UTC  
> Updated at: 2023-04-24 06:41:36 UTC  
> Closed at: 2023-04-24 06:41:35 UTC  
> Url: https://github.com/boostorg/process/issues/309  

The documentation for `boost::process::shell` says it's a property that can be used to launch processes via a system shell, but when I try to use it like this:  
```cpp  
      processImplUptr_->processUptr_ = std::make_unique<boost::process::child>(  
        boost::process::shell(errorCode),  
        boost::process::exe(rustDedicatedPath_),  
        boost::process::args(rustDedicatedArguments_),  
        boost::process::start_dir(workingDirectory_),  
        // I/O redirect options and effects:  
        // - do nothing: server takes over our console and garbles it up  
        // - close any/all: server spams its logs with exceptions  
        // - redirect any/all to null: server logs some things twice  
        boost::process::std_in  < boost::process::null,  
        boost::process::std_out > boost::process::null,  
        boost::process::std_err > boost::process::null,  
        boost::process::error(errorCode),  
        boost::process::windows::show,  
        WindowsCreationFlags(  
          // disconnect child process from Ctrl+C signals issued to parent  
          boost::winapi::CREATE_NEW_PROCESS_GROUP_ |  
          boost::winapi::CREATE_NEW_CONSOLE_  
        )  
      );  
```  
...I get a massive explosion of errors in MSYS MinGW GCC:  
```make  
[build] In file included from C:/msys64/opt/vcpkg/installed/x64-mingw-static/include/boost/process/detail/execute_impl.hpp:26,  
[build]                  from C:/msys64/opt/vcpkg/installed/x64-mingw-static/include/boost/process/child.hpp:22,  
[build]                  from C:/msys64/opt/vcpkg/installed/x64-mingw-static/include/boost/process/async_system.hpp:22,  
[build]                  from C:/msys64/opt/vcpkg/installed/x64-mingw-static/include/boost/process.hpp:24,  
[build]                  from C:/msys64/home/bensh/projects/rustLaunchSite/Server.cpp:7:  
[build] C:/msys64/opt/vcpkg/installed/x64-mingw-static/include/boost/process/detail/windows/executor.hpp: In instantiation of 'void boost::process::detail::windows::executor<Char, Sequence>::on_setup_t::operator()(T&) const [with T = {anonymous}::WindowsCreationFlags; Char = wchar_t; Sequence = boost::fusion::joint_view<boost::fusion::tuple<boost::process::detail::windows::exe_cmd_init<wchar_t> >, boost::fusion::filter_view<const boost::fusion::tuple<boost::filesystem::path&, boost::process::detail::exe_setter_<wchar_t>&, boost::process::detail::arg_setter_<wchar_t, true>&, boost::process::detail::windows::start_dir_init<wchar_t>&, boost::process::detail::windows::null_in&, boost::process::detail::windows::null_out<1, -1>&, boost::process::detail::windows::null_out<2, -1>&, boost::process::detail::set_on_error&, {anonymous}::WindowsCreationFlags&>, boost::process::detail::is_initializer<mpl_::arg<-1> > > >]':  
[build] C:/msys64/opt/vcpkg/installed/x64-mingw-static/include/boost/fusion/algorithm/iteration/detail/for_each.hpp:36:32:   recursively required from 'constexpr void boost::fusion::detail::for_each_linear(const First&, const Last&, F&, mpl_::false_) [with First = boost::fusion::filter_iterator<boost::fusion::forward_traversal_tag, boost::fusion::vector_iterator<const boost::fusion::tuple<boost::filesystem::path&, boost::process::detail::exe_setter_<wchar_t>&, boost::process::detail::arg_setter_<wchar_t, true>&, boost::process::detail::windows::start_dir_init<wchar_t>&, boost::process::detail::windows::null_in&, boost::process::detail::windows::null_out<1, -1>&, boost::process::detail::windows::null_out<2, -1>&, boost::process::detail::set_on_error&, {anonymous}::WindowsCreationFlags&>, 0>, boost::fusion::vector_iterator<const boost::fusion::tuple<boost::filesystem::path&, boost::process::detail::exe_setter_<wchar_t>&, boost::process::detail::arg_setter_<wchar_t, true>&, boost::process::detail::windows::start_dir_init<wchar_t>&, boost::process::detail::windows::null_in&, boost::process::detail::windows::null_out<1, -1>&, boost::process::detail::windows::null_out<2, -1>&, boost::process::detail::set_on_error&, {anonymous}::WindowsCreationFlags&>, 9>, boost::process::detail::is_initializer<mpl_::arg<-1> > >; Last = boost::fusion::filter_iterator<boost::fusion::forward_traversal_tag, boost::fusion::vector_iterator<const boost::fusion::tuple<boost::filesystem::path&, boost::process::detail::exe_setter_<wchar_t>&, boost::process::detail::arg_setter_<wchar_t, true>&, boost::process::detail::windows::start_dir_init<wchar_t>&, boost::process::detail::windows::null_in&, boost::process::detail::windows::null_out<1, -1>&, boost::process::detail::windows::null_out<2, -1>&, boost::process::detail::set_on_error&, {anonymous}::WindowsCreationFlags&>, 9>, boost::fusion::vector_iterator<const boost::fusion::tuple<boost::filesystem::path&, boost::process::detail::exe_setter_<wchar_t>&, boost::process::detail::arg_setter_<wchar_t, true>&, boost::process::detail::windows::start_dir_init<wchar_t>&, boost::process::detail::windows::null_in&, boost::process::detail::windows::null_out<1, -1>&, boost::process::detail::windows::null_out<2, -1>&, boost::process::detail::set_on_error&, {anonymous}::WindowsCreationFlags&>, 9>, boost::process::detail::is_initializer<mpl_::arg<-1> > >; F = boost::process::detail::windows::executor<wchar_t, boost::fusion::joint_view<boost::fusion::tuple<boost::process::detail::windows::exe_cmd_init<wchar_t> >, boost::fusion::filter_view<const boost::fusion::tuple<boost::filesystem::path&, boost::process::detail::exe_setter_<wchar_t>&, boost::process::detail::arg_setter_<wchar_t, true>&, boost::process::detail::windows::start_dir_init<wchar_t>&, boost::process::detail::windows::null_in&, boost::process::detail::windows::null_out<1, -1>&, boost::process::detail::windows::null_out<2, -1>&, boost::process::detail::set_on_error&, {anonymous}::WindowsCreationFlags&>, boost::process::detail::is_initializer<mpl_::arg<-1> > > > >::on_setup_t; mpl_::false_ = mpl_::bool_<false>]'  
[build] C:/msys64/opt/vcpkg/installed/x64-mingw-static/include/boost/fusion/algorithm/iteration/detail/for_each.hpp:36:32:   required from 'constexpr void boost::fusion::detail::for_each_linear(const First&, const Last&, F&, mpl_::false_) [with First = boost::fusion::joint_view_iterator<boost::fusion::forward_traversal_tag, boost::fusion::vector_iterator<boost::fusion::tuple<boost::process::detail::windows::exe_cmd_init<wchar_t> >, 0>, boost::fusion::vector_iterator<boost::fusion::tuple<boost::process::detail::windows::exe_cmd_init<wchar_t> >, 1>, boost::fusion::filter_iterator<boost::fusion::forward_traversal_tag, boost::fusion::vector_iterator<const boost::fusion::tuple<boost::filesystem::path&, boost::process::detail::exe_setter_<wchar_t>&, boost::process::detail::arg_setter_<wchar_t, true>&, boost::process::detail::windows::start_dir_init<wchar_t>&, boost::process::detail::windows::null_in&, boost::process::detail::windows::null_out<1, -1>&, boost::process::detail::windows::null_out<2, -1>&, boost::process::detail::set_on_error&, {anonymous}::WindowsCreationFlags&>, 0>, boost::fusion::vector_iterator<const boost::fusion::tuple<boost::filesystem::path&, boost::process::detail::exe_setter_<wchar_t>&, boost::process::detail::arg_setter_<wchar_t, true>&, boost::process::detail::windows::start_dir_init<wchar_t>&, boost::process::detail::windows::null_in&, boost::process::detail::windows::null_out<1, -1>&, boost::process::detail::windows::null_out<2, -1>&, boost::process::detail::set_on_error&, {anonymous}::WindowsCreationFlags&>, 9>, boost::process::detail::is_initializer<mpl_::arg<-1> > > >; Last = boost::fusion::filter_iterator<boost::fusion::forward_traversal_tag, boost::fusion::vector_iterator<const boost::fusion::tuple<boost::filesystem::path&, boost::process::detail::exe_setter_<wchar_t>&, boost::process::detail::arg_setter_<wchar_t, true>&, boost::process::detail::windows::start_dir_init<wchar_t>&, boost::process::detail::windows::null_in&, boost::process::detail::windows::null_out<1, -1>&, boost::process::detail::windows::null_out<2, -1>&, boost::process::detail::set_on_error&, {anonymous}::WindowsCreationFlags&>, 9>, boost::fusion::vector_iterator<const boost::fusion::tuple<boost::filesystem::path&, boost::process::detail::exe_setter_<wchar_t>&, boost::process::detail::arg_setter_<wchar_t, true>&, boost::process::detail::windows::start_dir_init<wchar_t>&, boost::process::detail::windows::null_in&, boost::process::detail::windows::null_out<1, -1>&, boost::process::detail::windows::null_out<2, -1>&, boost::process::detail::set_on_error&, {anonymous}::WindowsCreationFlags&>, 9>, boost::process::detail::is_initializer<mpl_::arg<-1> > >; F = boost::process::detail::windows::executor<wchar_t, boost::fusion::joint_view<boost::fusion::tuple<boost::process::detail::windows::exe_cmd_init<wchar_t> >, boost::fusion::filter_view<const boost::fusion::tuple<boost::filesystem::path&, boost::process::detail::exe_setter_<wchar_t>&, boost::process::detail::arg_setter_<wchar_t, true>&, boost::process::detail::windows::start_dir_init<wchar_t>&, boost::process::detail::windows::null_in&, boost::process::detail::windows::null_out<1, -1>&, boost::process::detail::windows::null_out<2, -1>&, boost::process::detail::set_on_error&, {anonymous}::WindowsCreationFlags&>, boost::process::detail::is_initializer<mpl_::arg<-1> > > > >::on_setup_t; mpl_::false_ = mpl_::bool_<false>]'  
[build] C:/msys64/opt/vcpkg/installed/x64-mingw-static/include/boost/fusion/algorithm/iteration/detail/for_each.hpp:46:32:   required from 'constexpr void boost::fusion::detail::for_each_dispatch(Sequence&, F&, Tag) [with Sequence = boost::fusion::joint_view<boost::fusion::tuple<boost::process::detail::windows::exe_cmd_init<wchar_t> >, boost::fusion::filter_view<const boost::fusion::tuple<boost::filesystem::path&, boost::process::detail::exe_setter_<wchar_t>&, boost::process::detail::arg_setter_<wchar_t, true>&, boost::process::detail::windows::start_dir_init<wchar_t>&, boost::process::detail::windows::null_in&, boost::process::detail::windows::null_out<1, -1>&, boost::process::detail::windows::null_out<2, -1>&, boost::process::detail::set_on_error&, {anonymous}::WindowsCreationFlags&>, boost::process::detail::is_initializer<mpl_::arg<-1> > > >; F = boost::process::detail::windows::executor<wchar_t, boost::fusion::joint_view<boost::fusion::tuple<boost::process::detail::windows::exe_cmd_init<wchar_t> >, boost::fusion::filter_view<const boost::fusion::tuple<boost::filesystem::path&, boost::process::detail::exe_setter_<wchar_t>&, boost::process::detail::arg_setter_<wchar_t, true>&, boost::process::detail::windows::start_dir_init<wchar_t>&, boost::process::detail::windows::null_in&, boost::process::detail::windows::null_out<1, -1>&, boost::process::detail::windows::null_out<2, -1>&, boost::process::detail::set_on_error&, {anonymous}::WindowsCreationFlags&>, boost::process::detail::is_initializer<mpl_::arg<-1> > > > >::on_setup_t; Tag = boost::fusion::forward_traversal_tag]'  
[build] C:/msys64/opt/vcpkg/installed/x64-mingw-static/include/boost/fusion/algorithm/iteration/detail/for_each.hpp:143:34:   required from 'constexpr void boost::fusion::detail::for_each(Sequence&, F&, mpl_::false_) [with Sequence = boost::fusion::joint_view<boost::fusion::tuple<boost::process::detail::windows::exe_cmd_init<wchar_t> >, boost::fusion::filter_view<const boost::fusion::tuple<boost::filesystem::path&, boost::process::detail::exe_setter_<wchar_t>&, boost::process::detail::arg_setter_<wchar_t, true>&, boost::process::detail::windows::start_dir_init<wchar_t>&, boost::process::detail::windows::null_in&, boost::process::detail::windows::null_out<1, -1>&, boost::process::detail::windows::null_out<2, -1>&, boost::process::detail::set_on_error&, {anonymous}::WindowsCreationFlags&>, boost::process::detail::is_initializer<mpl_::arg<-1> > > >; F = boost::process::detail::windows::executor<wchar_t, boost::fusion::joint_view<boost::fusion::tuple<boost::process::detail::windows::exe_cmd_init<wchar_t> >, boost::fusion::filter_view<const boost::fusion::tuple<boost::filesystem::path&, boost::process::detail::exe_setter_<wchar_t>&, boost::process::detail::arg_setter_<wchar_t, true>&, boost::process::detail::windows::start_dir_init<wchar_t>&, boost::process::detail::windows::null_in&, boost::process::detail::windows::null_out<1, -1>&, boost::process::detail::windows::null_out<2, -1>&, boost::process::detail::set_on_error&, {anonymous}::WindowsCreationFlags&>, boost::process::detail::is_initializer<mpl_::arg<-1> > > > >::on_setup_t; mpl_::false_ = mpl_::bool_<false>]'  
[build] C:/msys64/opt/vcpkg/installed/x64-mingw-static/include/boost/fusion/algorithm/iteration/for_each.hpp:35:25:   required from 'constexpr typename boost::enable_if<boost::fusion::traits::is_sequence<Sequence> >::type boost::fusion::for_each(Sequence&, F) [with Sequence = joint_view<tuple<boost::process::detail::windows::exe_cmd_init<wchar_t> >, filter_view<const tuple<boost::filesystem::path&, boost::process::detail::exe_setter_<wchar_t>&, boost::process::detail::arg_setter_<wchar_t, true>&, boost::process::detail::windows::start_dir_init<wchar_t>&, boost::process::detail::windows::null_in&, boost::process::detail::windows::null_out<1, -1>&, boost::process::detail::windows::null_out<2, -1>&, boost::process::detail::set_on_error&, {anonymous}::WindowsCreationFlags&>, boost::process::detail::is_initializer<mpl_::arg<-1> > > >; F = boost::process::detail::windows::executor<wchar_t, joint_view<tuple<boost::process::detail::windows::exe_cmd_init<wchar_t> >, filter_view<const tuple<boost::filesystem::path&, boost::process::detail::exe_setter_<wchar_t>&, boost::process::detail::arg_setter_<wchar_t, true>&, boost::process::detail::windows::start_dir_init<wchar_t>&, boost::process::detail::windows::null_in&, boost::process::detail::windows::null_out<1, -1>&, boost::process::detail::windows::null_out<2, -1>&, boost::process::detail::set_on_error&, {anonymous}::WindowsCreationFlags&>, boost::process::detail::is_initializer<mpl_::arg<-1> > > > >::on_setup_t; typename boost::enable_if<traits::is_sequence<Sequence> >::type = void]'  
[build] C:/msys64/opt/vcpkg/installed/x64-mingw-static/include/boost/process/detail/windows/executor.hpp:179:32:   required from 'boost::process::child boost::process::detail::windows::executor<Char, Sequence>::operator()() [with Char = wchar_t; Sequence = boost::fusion::joint_view<boost::fusion::tuple<boost::process::detail::windows::exe_cmd_init<wchar_t> >, boost::fusion::filter_view<const boost::fusion::tuple<boost::filesystem::path&, boost::process::detail::exe_setter_<wchar_t>&, boost::process::detail::arg_setter_<wchar_t, true>&, boost::process::detail::windows::start_dir_init<wchar_t>&, boost::process::detail::windows::null_in&, boost::process::detail::windows::null_out<1, -1>&, boost::process::detail::windows::null_out<2, -1>&, boost::process::detail::set_on_error&, {anonymous}::WindowsCreationFlags&>, boost::process::detail::is_initializer<mpl_::arg<-1> > > >]'  
[build] C:/msys64/opt/vcpkg/installed/x64-mingw-static/include/boost/process/detail/execute_impl.hpp:267:16:   required from 'boost::process::child boost::process::detail::basic_execute_impl(Args&& ...) [with Char = wchar_t; Args = {boost::filesystem::path, exe_setter_<wchar_t>, arg_setter_<wchar_t, true>, windows::start_dir_init<wchar_t>, windows::null_in, windows::null_out<1, -1>, windows::null_out<2, -1>, set_on_error, {anonymous}::WindowsCreationFlags}]'  
[build] C:/msys64/opt/vcpkg/installed/x64-mingw-static/include/boost/process/detail/execute_impl.hpp:275:45:   required from 'boost::process::child boost::process::detail::execute_impl(Args&& ...) [with Args = {boost::filesystem::path, exe_setter_<char>, arg_setter_<char, true>, windows::start_dir_init<char>, windows::null_in, windows::null_out<1, -1>, windows::null_out<2, -1>, set_on_error, {anonymous}::WindowsCreationFlags}]'  
[build] C:/msys64/opt/vcpkg/installed/x64-mingw-static/include/boost/process/child.hpp:35:51:   required from 'boost::process::child::child(Args&& ...) [with Args = {boost::filesystem::path, boost::process::detail::exe_setter_<char>, boost::process::detail::arg_setter_<char, true>, boost::process::detail::windows::start_dir_init<char>, boost::process::detail::windows::null_in, boost::process::detail::windows::null_out<1, -1>, boost::process::detail::windows::null_out<2, -1>, boost::process::detail::set_on_error, {anonymous}::WindowsCreationFlags}]'  
[build] C:/msys64/mingw64/include/c++/12.2.0/bits/unique_ptr.h:1065:30:   required from 'std::__detail::__unique_ptr_t<_Tp> std::make_unique(_Args&& ...) [with _Tp = boost::process::child; _Args = {boost::filesystem::path, boost::process::detail::exe_setter_<char>, boost::process::detail::arg_setter_<char, true>, boost::process::detail::windows::start_dir_init<char>, boost::process::detail::windows::null_in, boost::process::detail::windows::null_out<1, -1>, boost::process::detail::windows::null_out<2, -1>, boost::process::detail::set_on_error, {anonymous}::WindowsCreationFlags}; __detail::__unique_ptr_t<_Tp> = __detail::__unique_ptr_t<boost::process::child>]'  
[build] C:/msys64/home/bensh/projects/rustLaunchSite/Server.cpp:302:77:   required from here  
[build] C:/msys64/opt/vcpkg/installed/x64-mingw-static/include/boost/process/detail/windows/executor.hpp:135:27: error: no matching function for call to '{anonymous}::WindowsCreationFlags::on_setup(boost::process::detail::windows::executor<wchar_t, boost::fusion::joint_view<boost::fusion::tuple<boost::process::detail::windows::exe_cmd_init<wchar_t> >, boost::fusion::filter_view<const boost::fusion::tuple<boost::filesystem::path&, boost::process::detail::exe_setter_<wchar_t>&, boost::process::detail::arg_setter_<wchar_t, true>&, boost::process::detail::windows::start_dir_init<wchar_t>&, boost::process::detail::windows::null_in&, boost::process::detail::windows::null_out<1, -1>&, boost::process::detail::windows::null_out<2, -1>&, boost::process::detail::set_on_error&, {anonymous}::WindowsCreationFlags&>, boost::process::detail::is_initializer<mpl_::arg<-1> > > > >&)'  
[build]   135 |                 t.on_setup(exec);  
[build]       |                 ~~~~~~~~~~^~~~~~  
[build] C:/msys64/home/bensh/projects/rustLaunchSite/Server.cpp:45:10: note: candidate: 'template<class Sequence> void {anonymous}::WindowsCreationFlags::on_setup(boost::process::extend::windows_executor<char, Sequence>&)'  
[build]    45 |     void on_setup(boost::process::extend::windows_executor<char, Sequence> & ex)  
[build]       |          ^~~~~~~~  
[build] C:/msys64/home/bensh/projects/rustLaunchSite/Server.cpp:45:10: note:   template argument deduction/substitution failed:  
[build] C:/msys64/opt/vcpkg/installed/x64-mingw-static/include/boost/process/detail/windows/executor.hpp:135:27: note:   mismatched types 'char' and 'wchar_t'  
[build]   135 |                 t.on_setup(exec);  
[build]       |                 ~~~~~~~~~~^~~~~~  
```  
  
What's going on here?

---

## Comment 1

> Username: HunterZ  
> Created at: 2023-04-24 06:38:13 UTC  
> Url: https://github.com/boostorg/process/issues/309#issuecomment-1519472464  

Actually, I think this is something to do with my attempt to provide an extend handler that allows me to modify creation flags, but I have no idea how to fix it. It doesn't have problems unless I try to mix it with use of `boost::process::shell`.  
```cpp  
  // boost::process extension to launch a process in an ew console window  
  // idea from https://stackoverflow.com/a/69774875/3171290 and  
  //  https://stackoverflow.com/a/68751737/3171290  
  struct WindowsCreationFlags : boost::process::extend::handler  
  {  
    boost::winapi::DWORD_ flags_{0};  
  
    explicit WindowsCreationFlags(const boost::winapi::DWORD_ flags)  
      : flags_(flags)  
    {  
    }  
  
    // this function will be invoked at child process constructor before spawning process  
    template <typename Sequence>  
    void on_setup(boost::process::extend::windows_executor<char, Sequence> & ex)  
    {  
      ex.creation_flags |= flags_;  
      std::cout << "Modified Windows process creation flags: " << ex.creation_flags << std::endl;  
      std::cout << "Modified Windows handle inheritance: " << ex.inherit_handles << std::endl;  
    }  
  };  
```

---

## Comment 2

> Username: HunterZ  
> Created at: 2023-04-24 06:41:35 UTC  
> Url: https://github.com/boostorg/process/issues/309#issuecomment-1519475333  

Never mind, I fixed it by tweaking the extend handler template signature to match what I see in Boost headers:  
```cpp  
    template <typename Char, typename Sequence>  
    void on_setup(boost::process::extend::windows_executor<Char, Sequence> & ex)  
```
