# #42 - GHA CMake tests disabled: broken [Open]

> Username: jeking3  
> Created at: 2024-11-30 04:32:06 UTC  
> Updated at: 2024-11-30 04:32:06 UTC  
> Url: https://github.com/boostorg/interval/issues/42  

```  
Run cmake_test_folder="$BOOST_ROOT/libs/$SELF/test/cmake_test" # New unified folder  
  cmake_test_folder="$BOOST_ROOT/libs/$SELF/test/cmake_test" # New unified folder  
  [ -d "$cmake_test_folder" ] || cmake_test_folder="$BOOST_ROOT/libs/$SELF/test/cmake_subdir_test"  
  cd "$cmake_test_folder"  
  mkdir __build_cmake_subdir_test__ && cd __build_cmake_subdir_test__  
  cmake -G "Unix Makefiles" -DBOOST_CI_INSTALL_TEST=OFF -DCMAKE_BUILD_TYPE=Debug -DBUILD_SHARED_LIBS=ON ..  
  cmake --build . --config Debug -j$B2_JOBS  
  ctest --output-on-failure --build-config Debug  
  shell: /usr/bin/bash --noprofile --norc -e -o pipefail {0}  
  env:  
    ASAN_OPTIONS: allocator_may_return_null=1  
    GIT_FETCH_JOBS: 8  
    NET_RETRY_COUNT: 5  
    B2_CI_VERSION: 1  
    B2_VARIANT: debug,release  
    B2_LINK: shared,static  
    LCOV_BRANCH_COVERAGE: 1  
    CODECOV_NAME: Github Actions  
    BOOST_CI_TARGET_BRANCH: ci  
    BOOST_CI_SRC_FOLDER: /home/runner/work/interval/interval  
    SELF: numeric/interval  
    BOOST_ROOT: /home/runner/work/interval/boost-root  
    B2_TOOLSET:   
    B2_COMPILER:   
    B2_CXXSTD: 11  
    B2_JOBS: 5  
-- The CXX compiler identification is GNU 9.4.0  
-- Detecting CXX compiler ABI info  
-- Detecting CXX compiler ABI info - done  
-- Check for working CXX compiler: /usr/bin/c++ - skipped  
-- Detecting CXX compile features  
-- Detecting CXX compile features - done  
CMake Error at CMakeLists.txt:15 (add_subdirectory):  
  The source directory  
  
    /home/runner/work/interval/boost-root/libs  
  
  does not contain a CMakeLists.txt file.  
  
  
-- Configuring incomplete, errors occurred!  
Error: Process completed with exit code 1.  
```
