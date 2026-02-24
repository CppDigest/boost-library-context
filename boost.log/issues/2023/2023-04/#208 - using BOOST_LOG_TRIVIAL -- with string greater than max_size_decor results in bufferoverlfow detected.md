# #208 - using BOOST_LOG_TRIVIAL << with string greater than max_size_decor results in bufferoverlfow detected [Closed]

> Username: JM-ET  
> Created at: 2023-04-05 18:26:05 UTC  
> Updated at: 2023-04-06 21:34:57 UTC  
> Closed at: 2023-04-05 20:01:18 UTC  
> Url: https://github.com/boostorg/log/issues/208  

Hi All.  
  
I have an existing service which recently had its base runtime image/os updated from alpine 3:12 to ubuntu 22.04  
Its C++, uses gRPC/protobuf. It uses Boost 1.62.   
It is configured with boost::log::expressions::max_size_decor<char>(10000, "--truncated--")  
  
On the server side, it takes a gRPC request which has a large (14k bytes) nested member in the gRPC Message.   
  
The existing code calls BOOST_LOG_TRIVIAL (info) << request->ShortDebugString()  
  
This is essentially a method which returns a string representation of the entire gRPC request, which contained within has one part which alone is over 14k bytes in size.  
  
Following the image/os update, this same service reports buffer overflow:  
  
/usr/app/entrypoint.sh: line 10:    11 Aborted                 (core dumped)  
  
I've inspected the core dump but it contains no stack trace.  
  
If I change the boost::log::expressions::max_size_decor<char>(10000, "--truncated--") to 20000, then if works.  
  
However I am at odds, with why this is fundamentally happening.  
  
Boost 1.72 was installed on the original image using apk and in the new os/image, Boost 1.74 is installed using apt-get.  
  
I have searched online as far as I can for anyone discussing the above.  
In the absence of a stack trace, I cannot pin what is occurring when I try to stream a string larger than max_size_decor to BOOST_LOG_TRIVIA, that would result in coredump.

---

## Comment 1

> Username: Lastique  
> Created at: 2023-04-05 19:54:15 UTC  
> Url: https://github.com/boostorg/log/issues/208#issuecomment-1498036265  

You probably need to install debug symbol packages to obtain useful stacktraces.  
  
It is possible that you are affected by [this](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=105857) libstdc++ bug. A [workaround](https://github.com/boostorg/log/commit/269cedff6cdb81711e70e1202fddb67520012931) for it was added in Boost 1.80.  
  
Otherwise, without a stacktrace or, better yet, a small reproducer, I cannot help, sorry.

---

## Comment 2

> Username: JM-ET  
> Created at: 2023-04-06 19:34:23 UTC  
> Url: https://github.com/boostorg/log/issues/208#issuecomment-1499524052  

Thanks you Andrey.  
I spent a good while compiling 1_80 from source (prev service used boost  
1_74 using apt install).  
However, upon starting the service, it would core dump citing:  
  
terminate called after throwing an instance of  
'boost::wrapexcept<boost::filesystem::filesystem_error>'  
what(): Failed to open file for writing: Input/Output error [generic:5]:  
"/dev/stdout"  
  
I have no idea why.  
  
I decided instead to port the workaround onto 1_74 src (generated a patch  
file to make the change in  
https://github.com/boostorg/log/commit/269cedff6cdb81711e70e1202fddb67520012931)  
and then build that:  
  
RUN wget  
https://boostorg.jfrog.io/artifactory/main/release/1.74.0/source/boost_1_74_0.tar.gz  
&& \  
    tar xf boost_1_74_0.tar.gz && \  
    mkdir -p /home/builder/boost_1_74_0/OUT  
WORKDIR /home/builder/boost_1_74_0  
COPY boost_1_74_0.patch .  
RUN patch boost/log/detail/attachable_sstream_buf.hpp boost_1_74_0.patch  
RUN ./bootstrap.sh --prefix=/home/builder/boost_1_74_0/OUT && \  
     ./b2 -q -j4 toolset=gcc link=static address-model=64  
cxxflags=-std=c++11 threading=multi variant=debug inlining=off  
debug-symbols=on install && \  
    sudo rsync -a OUT/ /usr/  
  
And now the service, respects the  
boost::log::expressions::max_size_decor<char>(10000, "--truncated--")  
  
  
10000 limit and introduces "--truncated--" at end of stream, rather than  
coredumping as it did before when it encountered a string greater than  
10000 chars.  
  
  
Thank you for your assistance and for guiding me to a suitable workaround  
to proceed further.  
I will investigate further why 1_80 brings about coredump, once I've  
reviewed the diff/release notes between 1_74 and 1_80.  
  
Kind regards,  
  
John Mayock  
  
On Wed, 5 Apr 2023 at 20:54, Andrey Semashev ***@***.***>  
wrote:  
  
> You probably need to install debug symbol packages to obtain useful  
> stacktraces.  
>  
> It is possible that you are affected by this  
> <https://gcc.gnu.org/bugzilla/show_bug.cgi?id=105857> libstdc++ bug. A  
> workaround  
> <https://github.com/boostorg/log/commit/269cedff6cdb81711e70e1202fddb67520012931>  
> for it was added in Boost 1.80.  
>  
> Otherwise, without a stacktrace or, better yet, a small reproducer, I  
> cannot help, sorry.  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/log/issues/208#issuecomment-1498036265>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AOEXKYDBB2IDVC3OCYLTH6TW7XEXDANCNFSM6AAAAAAWUNODYU>  
> .  
> You are receiving this because you authored the thread.Message ID:  
> ***@***.***>  
>  
  
  
--   
John Mayock  
***@***.*** ***@***.***>*

---

## Comment 3

> Username: Lastique  
> Created at: 2023-04-06 21:31:43 UTC  
> Updated at: 2023-04-06 21:34:57 UTC  
> Url: https://github.com/boostorg/log/issues/208#issuecomment-1499653263  

> terminate called after throwing an instance of 'boost::wrapexcept<boost::filesystem::filesystem_error>' what(): Failed to open file for writing: Input/Output error [generic:5]: "/dev/stdout"  
  
This exception is [produced](https://github.com/boostorg/log/blob/f6808349bfd91f39ec18cc8ef25d1e1a3104d149/src/text_file_backend.cpp#L1474-L1477) by `text_file_backend` when it fails to open the log file for writing, and "/dev/stdout" is the file name. Using `text_file_backend` for console output is rather unconventional, as this backend is intended to be used with regular files. In particular, it assumes the file is [seekable](https://github.com/boostorg/log/blob/f6808349bfd91f39ec18cc8ef25d1e1a3104d149/src/text_file_backend.cpp#L1634-L1637). It may work in some limited scenarios, when features like file rotation and appending are not used, but this would be by accident and it is not the intended use case. You should normally use `text_ostream_backend` with `std::cout` or `std::clog` for console output.
