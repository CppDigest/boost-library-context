# #39 - `exit_code()` not set when using asio handling [Closed]

> Username: matthijskooijman  
> Created at: 2018-04-16 14:02:43 UTC  
> Updated at: 2019-04-10 02:18:19 UTC  
> Closed at: 2019-04-10 02:18:19 UTC  
> Url: https://github.com/boostorg/process/issues/39  

When an `ios` is passed to `bp:child`, the `io_ref_context` created from that is, AFAICS, intended to handle `SIGCHLD` and wait for the child pid. However, it seems this does not happen currently, and no meaningful exit code is subsequently reported by `exit_code()`. E.g. running the following example, shows `127` instead of the expected `0`:  
  
        boost::asio::io_service ios;  
        std::vector<char> buf;  
  
        bp::child c("/bin/true", bp::std_out > boost::asio::buffer(buf), ios);  
  
        ios.run();  
        int result = c.exit_code();  
        std::cout << result;  
  
Investigation on my actual case (which used `bp::system` and `std::future`, but should be identical in terms of this problem) showed that the `io_ref_context::on_success()` is run, but decides that there are no async handlers (e.g. `on_exit`) and there is no need to handle `SIGCHLD`. However, it seems that it should always wait for `sigchld()` in order to capture the exit status.  
  
Removing this check fixes the problem for me and seems like the right fix, but I'm not sure if there are any side effects. I can prepare a PR if that's helpful.  
  
https://github.com/boostorg/process/blob/35fda5aa6aa3d03051cc7c208ce0fd37aac4e9a3/include/boost/process/detail/posix/io_context_ref.hpp#L89-L90  
  
It seems that the Windows implementation has the same problem, though I can't test that:   
https://github.com/boostorg/process/blob/35fda5aa6aa3d03051cc7c208ce0fd37aac4e9a3/include/boost/process/detail/windows/io_context_ref.hpp#L91-L94

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2018-04-16 14:34:53 UTC  
> Url: https://github.com/boostorg/process/issues/39#issuecomment-381620761  

Thanks, that check is indeed errornous, a PR would be helpful. Could you fork from github.com/klemens-morgenstern/boost-process because I've only got the CI stuff set up there?

---

## Comment 2

> Username: matthijskooijman  
> Created at: 2018-04-16 15:40:05 UTC  
> Url: https://github.com/boostorg/process/issues/39#issuecomment-381649682  

Ok, done: https://github.com/klemens-morgenstern/boost-process/pull/161  
  
Slightly confusing to find another repo with issues and PRs, any plans to migrate everything to this repo?

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2018-04-17 08:44:42 UTC  
> Url: https://github.com/boostorg/process/issues/39#issuecomment-381903551  

I actually would like to have everything at my personal one and this one to be a fork of that, like it's with boost.dll. I don't have admin rights on this repo, which is why I can't even activate travis/appveyor.
