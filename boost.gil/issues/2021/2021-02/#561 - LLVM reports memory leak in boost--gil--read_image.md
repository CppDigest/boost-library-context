# #561 - LLVM reports memory leak in boost::gil::read_image [Closed]

> Username: nathanowen42  
> Created at: 2021-02-11 07:03:37 UTC  
> Updated at: 2022-06-27 21:34:26 UTC  
> Closed at: 2022-06-27 20:59:52 UTC  
> Url: https://github.com/boostorg/gil/issues/561  

### Actual behavior  
  
llvm reports a 1 byte memory leak in boost::gil::read_image when provided with a valid jpeg image.  This was produced when using a `boost::gil::any_image<boost::gil::gray8_image_t, boost::gil::gray16_image_t, boost::gil::rgb8_image_t, boost::gil::rgb16_image_t>`   
  
### Expected  behavior  
  
There should not be a memory leak in this case.  
  
### C++ Minimal Working Example  
  
<!-- C++ program or snippet that can be compiled and used to reproduce your problem -->  
  
```cpp  
#include <iostream>  
#include <vector>  
#include <sstream>  
  
#include <boost/gil.hpp>  
#include <boost/gil/extension/dynamic_image/any_image.hpp>  
#include <boost/gil/extension/io/jpeg.hpp>  
  
std::vector<uint8_t> load_jpeg(const std::string& filepath)  
{  
    std::vector<uint8_t> data;  
  
    try  
    {  
        boost::gil::any_image<boost::gil::gray8_image_t, boost::gil::gray16_image_t, boost::gil::rgb8_image_t,  
            boost::gil::rgb16_image_t> image;  
        boost::gil::read_image(filepath, image, boost::gil::jpeg_tag());  
        std::ostringstream out;  
        boost::gil::write_view(out, boost::gil::view(image), boost::gil::jpeg_tag());  
        auto output = out.str();  
        data.resize(output.size());  
        std::copy(std::begin(output), std::end(output), data.data());  
    }  
    catch (std::ios_base::failure& err)  
    {  
        std::cerr << "failed to get jpeg data for " << filepath << ": " << err.what() << std::endl;  
        return {};  
    }  
  
    return data;  
}  
  
int main()  
{  
    auto data = load_jpeg("./sample.jpeg");  
    std::cout << "loaded " << data.size() << " bytes";  
}  
```  
  
### Environment  
  
<!-- Any details that may help GIL maintainers help you -->  
  
- Compiler version:  
gcc version 10.2.1 20201125 (Red Hat 10.2.1-9) (GCC)  
clang version 10.0.1 (Fedora 10.0.1-3.fc32)  
  
- Build settings:  
  
Docker build/test environment:  
```  
#!/bin/bash  
DOCKER_TAG=boost_test  
docker build -t "${DOCKER_TAG}" - <<EOT  
FROM fedora:32  
RUN sudo dnf install -y wget gcc-c++ clang libjpeg-turbo-devel  
RUN sudo dnf clean all  
RUN groupadd -g 1000 build && useradd -u 1000 -g 1000 -ms /bin/bash build && usermod -a -G wheel build && usermod -a -G users build  
WORKDIR /tmp/build  
RUN wget 'https://dl.bintray.com/boostorg/release/1.75.0/source/boost_1_75_0.tar.gz'  
RUN tar xfvz boost_1_75_0.tar.gz  
WORKDIR /tmp/build/boost_1_75_0  
RUN ./bootstrap.sh --with-libraries=all --with-toolset=gcc --prefix=/usr/lib/boost_1_75_0  
RUN ./b2 debug install  
EOT  
  
if [ $? == 0 ] ; then  
    docker run --rm -it -v `pwd`:`pwd` -w `pwd` --rm --name build-env --user=build "${DOCKER_TAG}"  
fi  
```  
Build script:  
```  
#!/bin/bash  
export LIBRARY_PATH=$LIBRARY_PATH:/usr/lib/boost_1_75_0  
clang++ -O1 -g -I/usr/lib/boost_1_75_0/include -fsanitize=address -fno-omit-frame-pointer -c test_gil.cpp  
clang++ -g -L/usr/lib/boost_1_75_0/lib -ljpeg -fsanitize=address test_gil.o -o test_gil  
ASAN_OPTIONS=detect_leaks=1 ./test_gil  
```  
  
- Version (`<boost/version.hpp>`):  107500
