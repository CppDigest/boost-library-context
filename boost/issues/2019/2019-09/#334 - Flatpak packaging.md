# #334 - Flatpak packaging [Closed]

> Username: tallero  
> Created at: 2019-09-28 01:30:29 UTC  
> Updated at: 2019-09-28 01:33:38 UTC  
> Closed at: 2019-09-28 01:33:38 UTC  
> Url: https://github.com/boostorg/boost/issues/334  

Just wanted to report a working [`boost.json`](https://github.com/tallero/misc/blob/master/boost.json)[[1](https://github.com/flathub/org.qbittorrent.qBittorrent/blob/master/org.qbittorrent.qBittorrent.yaml)][[2](https://github.com/boostorg/build/issues/194#issuecomment-305787330)] flatpak manifest with Boost.python built and installed:  
  
```json  
  
{  
  "name": "boost",  
  "disabled": false,  
  "buildsystem": "simple",  
  "build-options": {  
    "env":{  
      "BOOST_BUILD_PATH":"/app/boost_build"  
    }  
  },  
  "build-commands": [  
      "mkdir -p ${FLATPAK_DEST}/include/boost",  
      "PATH=$PATH:${BOOST_BUILD_PATH}/bin &&   
       cd tools/build &&   
       ./bootstrap.sh &&   
       ./b2 install --prefix=${FLATPAK_DEST}/boost_build &&   
       CPLUS_INCLUDE_PATH=$CPLUS_INCLUDE_PATH:/usr/include/python3.7m/ &&   
       cd ../../ &&   
       b2 toolset=gcc stage &&  
       cp -r ./stage/* ${FLATPAK_DEST}/ &&   
       echo 'using python : : /usr/bin/python3.7 ;' >> ${FLATPAK_DEST}/boost_build/user-config.jam &&   
       b2 --with-python toolset=gcc stage &&   
       cp -r ./stage/* ${FLATPAK_DEST}/ &&   
       cp -r boost/* ${FLATPAK_DEST}/include/boost",  
      "rm -r ${FLATPAK_DEST}/boost_build"  
      ],  
  "sources": [  
    {  
      "type": "archive",  
      "url": "https://dl.bintray.com/boostorg/release/1.71.0/source/boost_1_71_0.tar.bz2",  
      "sha256": "d73a8da01e8bf8c7eda40b4c84915071a8c8a0df4a6734537ddde4a8580524ee"  
    }  
  ],  
  "cleanup": [  
    "lib/debug"  
  ]  
}  
```
