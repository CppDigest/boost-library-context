# #104 - (Regression) In recent versions of Boost, simple file sink log is created as 00000.log instead of sample.log in the basic tutorial example [Closed]

> Username: joanbm  
> Created at: 2020-03-03 14:42:26 UTC  
> Updated at: 2020-03-04 01:09:49 UTC  
> Closed at: 2020-03-03 15:31:50 UTC  
> Url: https://github.com/boostorg/log/issues/104  

# Explanation  
  
I was just following the official Boost.Log tutorial and when reading the [setting up sinks](https://www.boost.org/doc/libs/1_72_0/libs/log/doc/html/log/tutorial/sinks.html) section, I stumbled upon an unexpected behaviour. In particular, when setting up a simple log file sink like in the tutorial as follows:  
  
    void init()  
    {  
        logging::add_file_log("sample.log");  
        [...]  
    }  
  
The log is created as `00000.log` instead of `sample.log` on my system (Arch Linux with Boost 1.72.0). After some research I have found another user with the same problem on the [following StackOverflow post](https://stackoverflow.com/questions/58884712/boost-log-file-cannot-create-sample-log-file) which points out that **this behaviour doesn't occur in previous versions of Boost.Log**. I can reproduce this observation and can't find no mention of relevant changes in the changelog, so this looks like this is a regression.  
  
# Test case  
  
I have made a simple Dockerfile to easily reproduce the problem and confirm this is a behaviour change between Boost 1.69.0 (`sample.log`) and Boost 1.71.0 (`00000.log`):  
  
    FROM alpine:3.11  
  
    RUN apk add boost-dev g++  
  
    RUN printf '\n\  
    /*\n\  
     *          Copyright Andrey Semashev 2007 - 2015.\n\  
     * Distributed under the Boost Software License, Version 1.0.\n\  
     *    (See accompanying file LICENSE_1_0.txt or copy at\n\  
     *          http://www.boost.org/LICENSE_1_0.txt)\n\  
     */\n\  
    \n\  
    \x23include <boost/log/core.hpp>\n\  
    \x23include <boost/log/trivial.hpp>\n\  
    \x23include <boost/log/expressions.hpp>\n\  
    \x23include <boost/log/sinks/text_file_backend.hpp>\n\  
    \x23include <boost/log/utility/setup/file.hpp>\n\  
    \x23include <boost/log/utility/setup/common_attributes.hpp>\n\  
    \x23include <boost/log/sources/severity_logger.hpp>\n\  
    \x23include <boost/log/sources/record_ostream.hpp>\n\  
    \n\  
    namespace logging = boost::log;\n\  
    namespace src = boost::log::sources;\n\  
    namespace sinks = boost::log::sinks;\n\  
    namespace keywords = boost::log::keywords;\n\  
    \n\  
    void init()\n\  
    {\n\  
        logging::add_file_log("sample.log");\n\  
    \n\  
        logging::core::get()->set_filter\n\  
        (\n\  
            logging::trivial::severity >= logging::trivial::info\n\  
        );\n\  
    }\n\  
    \n\  
    int main(int, char*[])\n\  
    {\n\  
        init();\n\  
        logging::add_common_attributes();\n\  
    \n\  
        using namespace logging::trivial;\n\  
        src::severity_logger< severity_level > lg;\n\  
    \n\  
        BOOST_LOG_SEV(lg, trace) << "A trace severity message";\n\  
        BOOST_LOG_SEV(lg, debug) << "A debug severity message";\n\  
        BOOST_LOG_SEV(lg, info) << "An informational severity message";\n\  
        BOOST_LOG_SEV(lg, warning) << "A warning severity message";\n\  
        BOOST_LOG_SEV(lg, error) << "An error severity message";\n\  
        BOOST_LOG_SEV(lg, fatal) << "A fatal severity message";\n\  
    \n\  
        return 0;\n\  
    }\n\  
    ' > log_tutorial.cpp  
  
  
    RUN g++ -DBOOST_LOG_DYN_LINK log_tutorial.cpp -lboost_thread -lboost_log -lboost_log_setup  
  
    CMD ["sh", "-c", "./a.out && ls *.log"]  
  
---  
  
Run with `sudo docker build . -t boostlogtestcase && sudo docker run --rm -it boostlogtestcase`.  
Output (Alpine 3.11, Boost 1.71.0): `00000.log`  
If one changes the first line to read `FROM alpine:3.10` (Boost 1.69.0): `sample.log`  
  
# Workaround / Observation  
  
I have found that if one passes `target_file_name = sample.log`, then the log is created with the expected `sample.log` file name on recent versions of Boost, i.e.:  
  
    void init()  
    {  
        logging::add_file_log  
        (  
            keywords::file_name = "sample.log",  
            keywords::target_file_name = "sample.log"  
        );  
        [...]  
    }  
  
However, `target_file_name` was introduced in Boost 1.70.0, so this causes compilation problems in old versions of Boost. Additionally, the [documentation on `target_file_name`](https://www.boost.org/doc/libs/1_72_0/libs/log/doc/html/boost/log/add_file_log.html) seems to imply that this should not be necessary.

---

## Comment 1

> Username: Lastique  
> Created at: 2020-03-03 15:31:50 UTC  
> Url: https://github.com/boostorg/log/issues/104#issuecomment-594011156  

This is a Boost.Parameter issue https://github.com/boostorg/parameter/issues/97. Please, refer to that issue for further comments.

---

## Comment 2

> Username: joanbm  
> Created at: 2020-03-04 01:09:31 UTC  
> Updated at: 2020-03-04 01:09:49 UTC  
> Url: https://github.com/boostorg/log/issues/104#issuecomment-594253043  

Thanks for the quick response! I'll follow that issue.  
  
For anyone stumbling upon this and looking for a workaround, it appears that the problem only happens when you pass a single `file_name` parameter. If you pass more parameters, it doesn't seem to happen (and it does not necessarily have to be `target_file_name`, ignore this in the report, it was misleading).  
  
For example this compiles and works on both Boost 1.6x and Boost 1.7x:  
  
    logging::add_file_log  
    (  
        keywords::file_name = "sample.log",  
        keywords::format = "[%TimeStamp%]: %Message%"  
    );
