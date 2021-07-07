# iRIC automatic solver build tutorial

## Create a github account and create a new repository

Create an account on github and email us a notification of your user name.  We'll add you to the i-RIC organizational group and send you an invite to join.

  [https://github.com/](https://github.com/)


## Create directory c:\iricdev\lib and extract iricsdk-x.x.x.zip into it  

Download the latest [iricsdk](https://github.com/i-RIC/iriclib/releases/latest) (currently iricsdk-0.2.9.zip)
Open a command prompt window and execute the following commands:

```
> c:
> cd \
> mkdir iricdev\lib
> cd iricdev\lib
> curl -LO https://github.com/i-RIC/iriclib/releases/download/v0.2.9/iricsdk-0.2.9.zip
> 7z x iricsdk-0.2.9.zip
```

## Setup ssh key to authenticate

[Adding a new SSH key to your GitHub account](https://docs.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)

## Create a new repository for the solver

Once you've been added to the i-RIC group create a new repository starting with the word solver (ie solver.tutorial)

images/new_repo.png

and press 'Create repository'


## Checkout (clone) the repository

Start 'Git Bash' and use the following commands to clone the repository

```
$ mkdir -p source/repos
$ cd source/repos
$ git clone git@github.com:i-RIC/solver.tutorial.git
$ cd solver.tutorial
```

## Add source files and definition.xml to repository

Copy source files and definition.xml file into the `c:\Users\%USERNAME%\source\repos\solver.tutorial` (`~/source/repos/solver.tutorial`) directory.  Then use git to add and commit them.

```
$ git add .
$ git status
On branch main
Your branch is up to date with 'origin/main'.

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   iric/definition.xml
        new file:   source/RKparams.f90
[...]
        new file:   source/write_cgns_cell.f90
        new file:   source/zeta_bcs.f90

$ git commit -m"initial commit"
@todo (branch: init)
[main c08bdd8] initial commit
 187 files changed, 17914 insertions(+)
 create mode 100644 iric/definition.xml
 create mode 100644 source/RKparams.f90
[...]
 create mode 100644 source/write_cgns_cell.f90
 create mode 100644 source/zeta_bcs.f90
```

## Add files from [solver-template](https://github.com/i-RIC/solver-template) repository


From the [solver-template](https://github.com/i-RIC/solver-template) repository add `CMakeLists.txt` and `.github/workflows/build.yml` into your repository
@todo (branch: add-solver-template)

```
$ curl -LO https://raw.githubusercontent.com/i-RIC/solver-template/main/CMakeLists.txt
$ mkdir -p .github/workflows
$ cd .github/workflows
$ curl -LO https://raw.githubusercontent.com/i-RIC/solver-template/main/.github/workflows/build.yml
$ cd ../..
$ @todo git checkout -b add-solver-template
$ git add .github CMakeLists.txt
$ git commit -m"added solver-template files"
```

## Update CMakeLists.txt

Modify CMakeLists.txt file for the solver
Replace all occurrences of <BASENAME> with the basename of the executable you want to create (ie Example will create Example.exe).
  
```
$ code -n CMakeLists.txt
$ git diff
warning: LF will be replaced by CRLF in CMakeLists.txt.
The file will have its original line endings in your working directory
diff --git a/CMakeLists.txt b/CMakeLists.txt
index a7ea0ad..cbaf386 100644
--- a/CMakeLists.txt
+++ b/CMakeLists.txt
@@ -1,6 +1,6 @@
 cmake_minimum_required(VERSION 3.12)

-project(<BASENAME> Fortran)
+project(Example Fortran)

 include(GNUInstallDirs)

@@ -14,17 +14,17 @@ if(IRIC_SOLVER_USE_OPENMP)
   find_package(OpenMP REQUIRED)
 endif()

-add_executable(<BASENAME>
+add_executable(Example
   <ADD SOURCE FILES HERE>
 )

-target_link_libraries(<BASENAME> PRIVATE iriclib::iriclib)
+target_link_libraries(Example PRIVATE iriclib::iriclib)

 if(IRIC_SOLVER_USE_OPENMP AND OpenMP_Fortran_FOUND)
-  target_link_libraries(<BASENAME> PRIVATE OpenMP::OpenMP_Fortran)
+  target_link_libraries(Example PRIVATE OpenMP::OpenMP_Fortran)
 endif()

-install(TARGETS <BASENAME> DESTINATION "${CMAKE_INSTALL_BINDIR}")
+install(TARGETS Example DESTINATION "${CMAKE_INSTALL_BINDIR}")

 install(FILES <LOCATION OF definition.xml> DESTINATION "${CMAKE_INSTALL_BINDIR}")
$ git add CMakeLists.txt
$ git commit -am"replaced <BASENAME> with Example"  @todo note -am usage
``` 
  
## Enable OpenMP if needed

Turn on OpenMP if the solver requires it (the tutorial example (SToRM) doesn't need it but is added for completeness)
  change the line:
    option(IRIC_SOLVER_USE_OPENMP "Solver requires OpenMP" OFF)
  to
    option(IRIC_SOLVER_USE_OPENMP "Solver requires OpenMP" ON)

```
$ @todo code -n CMakeLists.txt
$ @todo git checkout -b "enabled-openmp"
$ git commit -am"enabled openmp"
```

## Add source files to CMakeLists.txt

Replace <ADD SOURCE FILES HERE> with all of your source files including header files (if necessary)
The paths are relative to the CMakeLists.txt file

```
$ @todo code -n CMakeLists.txt
$ @todo git checkout -b "added-source"
$ git commit -am"added source files to CMakeLists.txt"
```


## Update location of definition.xml in CMakeLists.txt file.


Replace <LOCATION OF definition.xml> with the relative path to your definition.xml
@todo (branch: added-source)

    $ @todo git checkout -b set-definition_xml-location
    $ code -n CMakeLists.txt
    $ git commit -am"set definition.xml location"


## Test cmake configure


Download the latest version of cmake if you don't already have it (at least version 3.12)
Open a new 'Command Prompt' window

```
> cd source\repos\solver.tutorial
> mkdir _build
> cd _build
> cmake -DCMAKE_PREFIX_PATH=c:/iricdev/lib/install/iriclib-0.2.9/lib/cmake/iriclib -DCMAKE_INSTALL_PREFIX=INSTALL ..
-- Building for: Visual Studio 16 2019
-- Selecting Windows SDK version 10.0.18362.0 to target Windows 10.0.18363.
-- The Fortran compiler identification is Intel 20.2.2.20210228
-- Check for working Fortran compiler: C:/Program Files (x86)/Intel/oneAPI/compiler/2021.2.0/windows/bin/intel64/ifort.exe
-- Check for working Fortran compiler: C:/Program Files (x86)/Intel/oneAPI/compiler/2021.2.0/windows/bin/intel64/ifort.exe - works
-- Detecting Fortran compiler ABI info
-- Detecting Fortran compiler ABI info - done
-- Determine Intel Fortran Compiler Implicit Link Path
-- Determine Intel Fortran Compiler Implicit Link Path - done
-- Checking whether C:/Program Files (x86)/Intel/oneAPI/compiler/2021.2.0/windows/bin/intel64/ifort.exe supports Fortran 90
-- Checking whether C:/Program Files (x86)/Intel/oneAPI/compiler/2021.2.0/windows/bin/intel64/ifort.exe supports Fortran 90 - yes
-- Found OpenMP_Fortran: -Qopenmp (found version "5.0")
-- Found OpenMP: TRUE (found version "5.0")
-- Configuring done
-- Generating done
-- Build files have been written to: C:/Users/charlton/source/repos/solver.tutorial/_build
```

## Build the solver

At this point you can either open the project within Visual Studio

```
> .\Example.sln
```
     
- Change the 'Solution Configurations' dialog from Debug to RelWithDebInfo
     
- Build->Build Solution (or press F7)
     
```
Build started...
1>------ Build started: Project: ZERO_CHECK, Configuration: Debug x64 ------
1>Checking Build System
2>------ Build started: Project: Example, Configuration: Debug x64 ------
2>Compiling with Intel® Fortran Compiler Classic 2021.2.0 [Intel(R) 64]...
2>sortEdges.f90
2>in_array2.f90
[...]
2>Example.dir\Debug\Example.exe.embed.manifest.res
2>LINK : C:\Users\charlton\source\repos\solver.tutorial\_build\Debug\Example.exe not found or not built by the last incremental link; performing full link
2>Embedding manifest...
2>Microsoft (R) Manifest Tool
2>Copyright (c) Microsoft Corporation.
2>All rights reserved.
2>
2>Build log written to  "file://C:\Users\charlton\source\repos\solver.tutorial\_build\Example.dir\Debug\BuildLog.htm"
2>Example - 0 error(s), 0 warning(s)
3>------ Build started: Project: ALL_BUILD, Configuration: Debug x64 ------
3>Building Custom Rule C:/Users/charlton/source/repos/solver.tutorial/CMakeLists.txt
4>------ Skipped Build: Project: INSTALL, Configuration: Debug x64 ------
4>Project not selected to build for this solution configuration 
========== Build: 3 succeeded, 0 failed, 0 up-to-date, 1 skipped ==========
```

- Right-Click on INSTALL->Build
```
Build started...
1>------ Build started: Project: INSTALL, Configuration: Debug x64 ------
1>-- Install configuration: "Debug"
1>-- Installing: C:/Users/charlton/source/repos/solver.tutorial/_build/INSTALL/bin/Example.exe
1>-- Installing: C:/Users/charlton/source/repos/solver.tutorial/_build/INSTALL/bin/definition.xml
========== Build: 1 succeeded, 0 failed, 3 up-to-date, 0 skipped ==========
```
         
Or build from the command line

```
> cmake --build . --config RelWithDebInfo
> cmake --build . --config RelWithDebInfo --target install
```

## Setup package directory

Go back to the 'Git Bash' window and edit `.github\workflows\build.yml`
(this tutorial uses storm)
```
$ code -n .github/workflows/build.yml
and replace <PACKAGE_DIR> with your corresponding package directory:

packages\solver.cap
packages\solver.ceri1d
packages\solver.dhabsim
packages\solver.elimo
packages\solver.evatrip
packages\solver.evatrip_pro
packages\solver.fastmech
packages\solver.mflow
packages\solver.morpho2dh
packages\solver.nays2d+
packages\solver.nays2db
packages\solver.nays2dflood
packages\solver.nays2dflood_dev
packages\solver.nays2dh
packages\solver.nays2dv
packages\solver.nays3dv
packages\solver.nayscube
packages\solver.naysdw2
packages\solver.nayseddy
packages\solver.naysmini
packages\solver.river2d
packages\solver.sac
packages\solver.srm
packages\solver.storm
packages\solver.utt
packages\solver.vel_estimate
```
    
## Have a repository secret added
     
Have someone with write-access to [oneline_update](https://github.com/i-RIC/online_update) add a repository secret (ONLINE_UPDATE_TOKEN) to the solver.

## Push to github and verify the actions tab

```
$ git push
```

Sample run: [https://github.com/i-RIC/solver.tutorial/runs/2879790345](https://github.com/i-RIC/solver.tutorial/runs/2879790345?check_suite_focus=true)
