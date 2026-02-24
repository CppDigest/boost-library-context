# #268 - Regex operations return incorrect results when ARM Memory Tagging Extensions (MTE) are enabled [Open]

> Username: mic-  
> Created at: 2026-01-21 17:01:17 UTC  
> Updated at: 2026-01-21 18:23:07 UTC  
> Url: https://github.com/boostorg/regex/issues/268  

Boost version:  
1.90.0. I've also seen this on 1.86.0, and don't think it's specific to any particular Boost version.  
  
Test hardware:  
Google Pixel 9  
  
Test OS:  
Android 16 QPR3 Beta 2 (build CP11.251209.007.A1).  
This build seems to have MTE enabled system-wide by default. Can be verified under Settings>System>Developer options>Memory Tagging Extension.  
  
Description:  
Boost regex operation return incorrect results, e.g. `boost::regex_match` returns false in cases when it should return true.  
This seems to occur when both of the following are true:  
1) Memory Tagging Extensions are enabled.  
2) A character type larger than 1 byte (e.g. `wchar_t`) is used.  
  
Steps to reproduce:  
I have prepared a minimal reproducible example.  
In Android Studio, select New>New project...>Native C++, and complete the wizard.  
  
Then replace the contents of the following files:  
  
app/src/main/cpp/native-lib.cpp  
```  
#include <jni.h>  
#include <string>  
#include "boost/regex.hpp"  
  
using CharT = wchar_t;  
using String = std::basic_string<CharT>;  
  
extern "C" JNIEXPORT jstring JNICALL  
Java_com_example_boostregex16qpr3b2_MainActivity_stringFromJNI(  
        JNIEnv* env,  
        jobject /* this */) {  
    String input = L"ABC";  
    String pattern = L"^[A-Z]{1,100}$";  // Note: 99 and below as the upper bound works for some reason.  
  
    boost::basic_regex<CharT, boost::cpp_regex_traits<CharT>> re;  
    re.imbue(std::locale::classic());  
    re.assign(pattern,boost::regex::perl);  
  
    if (boost::regex_match(input, re)) {  
        return env->NewStringUTF("OK");  
    } else {  
        return env->NewStringUTF("Fail");  
    }  
}  
```  
  
app/src/main/cpp/CMakeLists.txt  
```  
cmake_minimum_required(VERSION 3.22.1)  
  
project("boostregex16qpr3b2")  
  
set(BOOST_SRC_RELATIVE_PATH "" CACHE STRING "Path to the Boost source code, relative to app/src/main")  
get_filename_component(BOOSTSRCROOT ../${BOOST_SRC_RELATIVE_PATH} ABSOLUTE)  
  
set(CMAKE_CXX_STANDARD 20)  
set(CMAKE_CXX_STANDARD_REQUIRED ON)  
set(CMAKE_CXX_EXTENSIONS OFF)  
  
add_library(boost_headers INTERFACE)  
target_include_directories(boost_headers INTERFACE ${BOOSTSRCROOT})  
  
add_library(native-lib SHARED  
        native-lib.cpp  
)  
target_compile_options(native-lib PUBLIC  
        -funwind-tables -fomit-frame-pointer  
        -fno-strict-aliasing  
        -Wa,--noexecstack  
)  
target_link_options(native-lib PRIVATE "-Wl,-z,max-page-size=16384")  
target_link_libraries(native-lib  
        boost_headers  
        android  
        log)  
```  
  
app/build.gradle.kts  
```  
plugins {  
    alias(libs.plugins.android.application)  
    alias(libs.plugins.kotlin.android)  
}  
  
android {  
    namespace = "com.example.boostregex16qpr3b2"  
    compileSdk {  
        version = release(36)  
    }  
  
    ndkVersion = "27.3.13750724"  
  
    defaultConfig {  
        applicationId = "com.example.boostregex16qpr3b2"  
        minSdk = 28  
        targetSdk = 35  
        versionCode = 1  
        versionName = "1.0"  
  
        testInstrumentationRunner = "androidx.test.runner.AndroidJUnitRunner"  
        externalNativeBuild {  
            cmake {  
                arguments(  
                    "-DBOOST_SRC_RELATIVE_PATH=../../../../../../github/boost_1_90_0"  
                )  
            }  
        }  
    }  
  
    buildTypes {  
        release {  
            isMinifyEnabled = false  
            proguardFiles(  
                getDefaultProguardFile("proguard-android-optimize.txt"),  
                "proguard-rules.pro"  
            )  
        }  
    }  
    compileOptions {  
        sourceCompatibility = JavaVersion.VERSION_11  
        targetCompatibility = JavaVersion.VERSION_11  
    }  
    kotlinOptions {  
        jvmTarget = "11"  
    }  
    externalNativeBuild {  
        cmake {  
            path = file("src/main/cpp/CMakeLists.txt")  
            version = "3.22.1"  
        }  
    }  
    buildFeatures {  
        viewBinding = true  
    }  
}  
  
dependencies {  
    implementation(libs.androidx.core.ktx)  
    implementation(libs.androidx.appcompat)  
    implementation(libs.material)  
    implementation(libs.androidx.constraintlayout)  
    testImplementation(libs.junit)  
    androidTestImplementation(libs.androidx.junit)  
    androidTestImplementation(libs.androidx.espresso.core)  
}  
```  
  
Running the resulting app on the aforementioned Test device/OS will display "Fail" on the screen, instead of the expected "OK".
