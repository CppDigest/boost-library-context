# #1269 - Need Help Figuring Out Where Boost Libraries and Headers Are in Dockerfile-built Container [Closed]

> Username: DragonOsman  
> Created at: 2018-10-17 18:05:05 UTC  
> Updated at: 2018-11-28 02:09:38 UTC  
> Closed at: 2018-11-28 02:09:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1269  

I need help in figuring out where the Boost libraries got installed in the container. It seems like the headers are somewhere in `/usr/local/include`, but I want to know if they're in `/usr/local/include/boost`. And I also don't know where in `/usr/local/lib` the compiled library files are, exactly. Are they going to be in boost/stage/lib inside `/usr/local/lib`?   
Dockerfile:   
```  
FROM ubuntu:18.04  
WORKDIR /app  
COPY /scripts /styles index.html currency_converter.cpp /app/  
RUN apt-get update \  
  && apt-get install -y git \  
                        g++ \  
                        build-essential \  
                        make \  
                        wget \  
                        cmake  
RUN wget https://dl.bintray.com/boostorg/release/1.68.0/source/boost_1_68_0.tar.bz2 \  
    && tar -xjf boost_1_68_0.tar.bz2 \  
    && rm -rf boost_1_68_0.tar.bz2 \  
    && cd boost_1_68_0 \  
    && ./bootstrap.sh \   
    && ./b2 install  
RUN git clone https://github.com/flexferrum/jinja2cpp \   
    && cd jinja2cpp \  
    && git submodule -q update --init \   
    && mkdir build \   
    && cd build \  
    && cmake .. -DCMAKE_INSTALL_PREFIX=../install \  
    && cmake --build . --target all  
RUN git clone https://github.com/nlohmann/json  
RUN g++ -std=c++14 -Wall -pedantic -I app/jinja2cpp/install/include -I app/json/single_include -I /usr/local/include/ -I app/jinja2cpp/thirdparty/nonstd/value-ptr-lite/include currency_converter.cpp -L app/jinja2cpp/install/lib/static -ljinja2cpp -L usr/local/lib/stage/lib/ -lboost_system -lpthread -o currency_converter  
EXPOSE 80  
ENV apikey <APIKEY>  
ENV accesskey <ACCESSKEY>  
CMD ["./currency_converter", "0.0.0.0", "8080", "."]  
```  
Thanks for any replies and help.  
  
Edit: Okay, never mind; seems like the include files are in `usr/local/include` and library files `usr/local/lib`.  I saw it in the Dockerfile output when it was building Boost.  Sorry about this, then.

---

## Comment 1

> Username: djarek  
> Created at: 2018-10-17 19:25:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1269#issuecomment-430757039  

You should use a build system (like CMake), your current approach isn't maintainable in the long run.

---

## Comment 2

> Username: stale[bot]  
> Created at: 2018-11-27 01:09:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1269#issuecomment-441859437  

This issue has been open for a while with no activity, has it been resolved?
