# #730 - config_toolset.bat ingores toolset from bootstrap.bat and used the latest installed Visual Studio [Open]

> Username: SebTV  
> Created at: 2023-01-19 09:23:34 UTC  
> Updated at: 2023-01-19 09:23:34 UTC  
> Url: https://github.com/boostorg/boost/issues/730  

Issue: When specifying a toolset in bootstrap.bat, still the latest Visual Studio is used - at least to compile b2.  
This is because config_toolset.bat calles vswhere_usability_wrapper.cmd w/o parameters and vswhere_usability_wrapper.cmd returns the highest installed version first.  
  
Simple patch - just to show the workflow  
```  
--- boost_1_81_0/tools/build/src/engine/config_toolset.bat.org  
+++ boost_1_81_0/tools/build/src/engine/config_toolset.bat  
@@ -118,7 +118,7 @@  
   
 :Config_VC142  
 if not defined CXX ( set "CXX=cl" )  
-call vswhere_usability_wrapper.cmd  
+call vswhere_usability_wrapper.cmd %B2_TOOLSET%  
 REM Reset ERRORLEVEL since from now on it's all based on ENV vars  
 ver > nul 2> nul  
 if "_%B2_TOOLSET_ROOT%_" == "__" (  
@@ -140,7 +140,7 @@  
   
 :Config_VC143  
 if not defined CXX ( set "CXX=cl" )  
-call vswhere_usability_wrapper.cmd  
+call vswhere_usability_wrapper.cmd %B2_TOOLSET%  
 REM Reset ERRORLEVEL since from now on it's all based on ENV vars  
 ver > nul 2> nul  
 if "_%B2_TOOLSET_ROOT%_" == "__" (  
--- boost_1_81_0/tools/build/src/engine/vswhere_usability_wrapper.cmd.org  
+++ boost_1_81_0/tools/build/src/engine/vswhere_usability_wrapper.cmd  
@@ -17,6 +17,11 @@  
 set VSWHERE_REQ=-requires Microsoft.VisualStudio.Component.VC.Tools.x86.x64  
 set VSWHERE_PRP=-property installationPath  
   
+IF DEFINED %1% (  
+echo "Looking for vc maxium version %1%"  
+goto %1%  
+)  
+  
 REM Visual Studio Unknown Version, Beyond 2022  
 set VSWHERE_LMT=-version "[18.0,19.0)"  
 set VSWHERE_PRERELEASE=-prerelease  
@@ -30,6 +35,7 @@  
     exit /B 0  
 )  
   
+:vc143  
 REM Visual Studio 2022  
 set VSWHERE_LMT=-version "[17.0,18.0)"  
 set VSWHERE_PRERELEASE=-prerelease  
@@ -43,6 +49,7 @@  
     exit /B 0  
 )  
   
+:vc142  
 REM Visual Studio 2019 (16.X, toolset 14.2)  
 set VSWHERE_LMT=-version "[16.0,17.0)"  
 SET VSWHERE_ARGS=-latest -products * %VSWHERE_REQ% %VSWHERE_PRP% %VSWHERE_LMT% %VSWHERE_PRERELEASE%  
@@ -55,6 +62,7 @@  
     exit /B 0  
 )  
   
+:vc141  
 REM Visual Studio 2017 (15.X, toolset 14.1)  
 set VSWHERE_LMT=-version "[15.0,16.0)"  
 SET VSWHERE_ARGS=-latest -products * %VSWHERE_REQ% %VSWHERE_PRP% %VSWHERE_LMT%  
  
  
```
