# #1258 - Porting Beast web server app to WebAssembly (Not sure if this is okay to ask here) [Closed]

> Username: DragonOsman  
> Created at: 2018-09-27 20:44:10 UTC  
> Updated at: 2018-10-17 18:02:32 UTC  
> Closed at: 2018-10-17 18:02:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1258  

I'm done with building my application, but I'm having some trouble deploying (would be nice if I knew of a free web hosting site that accepted an app that also has its own web server where said server actually worked as intended--would also be good to have shell access so that I can set environment variables).  So I was wondering if I could port the web server app to WebAssembly.  I need to know if there's a way to execute the server app correctly in that case, like as a web server extension to Node.js or something like that.    
  
I read that WebAssembly requires networking code to be asynchronous, so I know I have to switch to the asynchronous HTTP server from the synchronous one.  First, though, I'll just show the code (as before, I have it on Gist).  
  
C++ code: https://gist.github.com/DragonOsman/d00ea7ec3d49c128ad7789293f156a09  
HTML code: https://gist.github.com/DragonOsman/506bd2bae1dfe4c03a4bbe2a6d830324  
CSS code: https://gist.github.com/DragonOsman/3f3ba56ffd58476c6defaea95b066dc2  
JavaScript code: https://gist.github.com/DragonOsman/c6e8fb15343544e662f474c5a526d1c2  
  
Is this a good question to ask here, or should I keep it to the WebAssembly support forums?  Hopefully I can at least find some web hosting advice here (note: I know neither my router's password nor how to find it, so I can't serve it from my own machine (can't do port forwarding)--which also crosses out reverse proxy).

---

## Comment 1

> Username: djarek  
> Created at: 2018-09-28 19:16:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1258#issuecomment-425538892  

I don't think Boost.ASIO supports web assembly at this time, so you might find it extremely hard to use that as a deployment option. Honestly, I think you're going to have better luck finding a service that accepts docker images. I know Heroku has a free plan (with a monthly runtime limit) and it accepts docker images, so you might have better luck with it.

---

## Comment 2

> Username: DragonOsman  
> Created at: 2018-09-28 23:04:17 UTC  
> Updated at: 2018-09-28 23:06:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1258#issuecomment-425589705  

I'll have to compile the code for Linux for that, right?  Right now I only have a `.exe` file.  And I tried to compile it on Docker but couldn't because of errors in compilation and getting the dependencies.  I also can't really compile it for Linux myself easily because I'd have to build Jinja2Cpp for Linux and I'm not familiar with using CMake on the command line.

---

## Comment 3

> Username: djarek  
> Created at: 2018-09-29 13:55:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1258#issuecomment-425646987  

A project that has a valid CMake list should be buildable (and installable) on Linux with  
```  
mkdir build  
cd build  
cmake -DCMAKE_BUILD_TYPE=Release ..   
cmake --build . --config Release --target install  
```  
  
When it comes to building boost, here's an example of how you might get boost in your docker image:  
https://github.com/djarek/boost-docker/blob/master/gcc/7/Dockerfile  
It's not ideal, but should get the job done. Note that that is a development/CI dockerfile, in a release image you might want to only install the release libraries (no headers).

---

## Comment 4

> Username: DragonOsman  
> Created at: 2018-09-29 23:00:32 UTC  
> Url: https://github.com/boostorg/beast/issues/1258#issuecomment-425680951  

Someone on a C++ GitHub server that I know gave me a Dockerfile before for my app, but when I tried to use it, I had some errors.  Nlohmann JSON's path is probably different from the one my code needs, for example.  And the file also doesn't include the static assets (HTML/CSS/JavaScript).    
  
This is that guy's message on GitHub from back then:  
```  
Here is the Dockerfile https://gist.github.com/kevinkjt2000/bcc9bdbf457007a47720b65f967c7e57  
Use it by building it docker build -t currency . and run it with something like docker run -d --name -p 8080:80 currency currency.  Logs can be read with docker logs currency.  I have to make a grocery run now, but I will gladly answer questions when I return if I see any here.  
```  
  
Error and warning messages from back then (when I tried to deploy my app to Docker with that Dockerfile:  
```  
[WARNING]: Empty continuation line found in:  
    RUN apk --no-cache add      boost-dev       cmake   git     g++     make  
[WARNING]: Empty continuation line found in:  
    RUN git clone --branch v${JINJA_2_CPP_VERSION} --depth 1    https://github.com/flexferrum/Jinja2Cpp.git /tmp/Jinja2Cpp  
[WARNING]: Empty continuation line found in:  
    RUN apk --no-cache add      boost-dev       g++  
[WARNING]: Empty continuation line found in:  
    RUN apk --no-cache add      boost-system    libstdc++  
[WARNING]: Empty continuation lines will become errors in a future release.  
  
Cloning into '/tmp/Jinja2Cpp'...  
Note: checking out '84af922ca27586b97123dcb7ee2306af5fabacfb'.  
  
You are in 'detached HEAD' state. You can look around, make experimental  
changes and commit them, and you can discard any commits you make in this  
state without impacting any branches by performing another checkout.  
  
If you want to create a new branch to retain commits you create, you may  
do so (now or later) by using -b with the checkout command again. Example:  
  
  git checkout -b <new-branch-name>  
  
Connecting to github.com (192.30.253.113:443)  
Connecting to github-production-release-asset-2e65be.s3.amazonaws.com (54.231.49.24:443)  
json.hpp               2% |                               | 16975   0:00:34 ETA  
json.hpp              37% |***********                    |   219k  0:00:03 ETA  
json.hpp             100% |*******************************|   581k  0:00:00 ETA  
  
currency_converter.cpp:28:29: fatal error: nlohmann/json.hpp: No such file or directory  
 #include <nlohmann/json.hpp>  
                             ^  
compilation terminated.  
```  
Could I see how to write this Dockerfile correctly, including the part for Boost that you linked?  I need to get all of the dependencies to work correctly, so: Boost.Beast (not standalone, and version 1.68.0), NlohmannJSON (https://github.com/nlohmann/json/ ), and Jinja2Cpp (https://github.com/flexferrum/Jinja2Cpp/ ).  I'm new to Docker and Dockerfiles, so please help me out here.    
  
Would be good if I could just deploy my `.exe` with the static assets as they are to a Docker image and use that, but I'm worried about whether I'll have to buy a license for a Windows Server image.

---

## Comment 5

> Username: djarek  
> Created at: 2018-10-02 20:14:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1258#issuecomment-426414039  

@DragonOsman you're missing installation step for nlohmann json. I'd also recommend writing a CMake file for your own project to get rid of the hassle of managing all these include/linker flags.

---

## Comment 6

> Username: DragonOsman  
> Created at: 2018-10-11 14:07:06 UTC  
> Url: https://github.com/boostorg/beast/issues/1258#issuecomment-428968988  

Nlohmann JSON is a header-only library, isn't it?  We just have to build tests and stuff, right?  
  
Anyway, I have a Dockerfile right now that I'm trying to use to build a Docker image.  I'm having problems building Boost.Build, though, and I don't know how to check bootstrap.log (it says to check bootstrap.log).    
  
Here's my Dockerfile:  
```  
FROM ubuntu:18.04  
WORKDIR /app  
COPY . /app  
USER root  
RUN apt-get update  
RUN apt-get install -y wget  
RUN wget https://dl.bintray.com/boostorg/release/1.68.0/source/boost_1_68_0.tar.bz2 \  
    && tar --bz2 -xf boost_1_68_0.tar.bz2 \  
    && cd boost_1_68_0 \  
    && ./bootstrap.sh \   
    && vim bootstrap.log \  
    && ./b2 install  
RUN apt-get install git  
RUN git clone https://github.com/flexferrum/jinja2cpp.git  
RUN git submodule -q update --init  
RUN cd jinja2cpp  
RUN mkdir build  
RUN cd build \  
    && cmake .. -DCMAKE_INSTALL_PREFIX=../install \  
    && cmake --build . --target all  
RUN git clone https://github.com/nlohmann/json.git  
RUN apt-get install g++  
RUN apt-get install build-essential  
RUN g++ -std=c++17 -Wall -pedantic -I /usr/local/jinja2cpp/install/include -I /usr/local/boost_1_68_0/ -I /usr/local/json/single_include/ -L /usr/local/jinja2cpp/build/ -llibjinja2cpp.a -L /usr/local/boost_1_68_0/stage/lib/ -llibboost_system.a currency_converter.cpp -o currency_converter  
EXPOSE 80  
ENV apikey **************************************************  
ENV accesskey ****************************************  
CMD ["./currency_converter", "0.0.0.0", "8080", "."]  
```  
[I hid the API Key and Access Key using those asterisks.  Don't mind that.]  
  
I also want to know if my include and library directory compiler flags are correct, but I can't worry about that until I've fixed the Boost installation issues.

---

## Comment 7

> Username: DragonOsman  
> Created at: 2018-10-11 15:39:38 UTC  
> Updated at: 2018-10-11 20:50:08 UTC  
> Url: https://github.com/boostorg/beast/issues/1258#issuecomment-429004039  

I looked around and made some changes to the Dockerfile:  
```  
FROM ubuntu:18.04  
WORKDIR /app  
COPY . /app  
USER root  
RUN apt-get update \  
  && apt-get install -y git \  
                        g++ \  
                        build-essential \  
                        make \  
                        wget  
RUN wget https://dl.bintray.com/boostorg/release/1.68.0/source/boost_1_68_0.tar.bz2 \  
    && tar -xjf boost_1_68_0.tar.bz2 \  
    && rm -rf boost_1_68_0.tar.bz2 \  
    && cd boost_1_68_0 \  
    && ./bootstrap.sh \   
    && cat bootstrap.log \  
    && ./b2 install  
RUN apt-get install git  
RUN git clone https://github.com/flexferrum/jinja2cpp  
RUN git submodule -q update --init  
RUN cd jinja2cpp  
RUN mkdir build  
RUN cd build \  
    && cmake .. -DCMAKE_INSTALL_PREFIX=../install \  
    && cmake --build . --target all  
RUN git clone https://github.com/nlohmann/json  
RUN g++ -std=c++17 -Wall -pedantic -I /usr/local/jinja2cpp/install/include -I /usr/local/boost_1_68_0/ -I /usr/local/json/single_include/ -L /usr/local/jinja2cpp/build/ -llibjinja2cpp.a -L /usr/local/boost_1_68_0/stage/lib/ -llibboost_system.a currency_converter.cpp -o currency_converter  
EXPOSE 80  
ENV apikey AIzaSyDpOhTTtJXh6EyO_iyH26tV5tSPTH5FgDo  
ENV accesskey 6118470d67d40caf1f4db130ec67e1e8  
CMD ["./currency_converter", "0.0.0.0", "8080", "."]  
```  
If the `cat` command works, I'll view that log file.  Hopefully I'll find a way to fix the problem.  
  
Edit: Changed it again.  There were other problems.  `./bootstrap.sh` works now, though.

---

## Comment 8

> Username: DragonOsman  
> Created at: 2018-10-17 16:56:38 UTC  
> Updated at: 2018-10-17 16:57:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1258#issuecomment-430706762  

Okay, I got that problem fixed but now I need help in figuring out where the Boost libraries got installed in the container.  It seems like the headers are somewhere in `/usr/local/include`, but I want to know if they're in `/usr/local/include/boost`.  And I also don't know where in `/usr/local/lib` the compiled library files are, exactly.  Are they going to be in `boost/stage/lib` inside `/usr/local/lib`?  Thanks for replies and help.  
  
Edit: Should I open a new issue for this?
