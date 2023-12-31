---
layout: post
auther: kabazoka
title: Building OpenCV with CMake and MinGW-make for C++ in VS Code
date: '2023-12-18 17:47:25 +0800'
---

> The following tutorial present the step to build and run a C++ program implementing OpenCV library in Windows 11.
> 

# 1. Installing CMake

---

[Download CMake](https://cmake.org/download/)

### If you are using the x64 cpu:

[https://github.com/Kitware/CMake/releases/download/v3.28.1/cmake-3.28.1-windows-x86_64.msi](https://github.com/Kitware/CMake/releases/download/v3.28.1/cmake-3.28.1-windows-x86_64.msi)

<aside>
💡 REMEMBER  to check the “Add CMake to the PATH for all users” during installation.

</aside>

### Confirm the installation has done properly

```bash
# type this in your terminal
cmake --version
```

![Untitled](/assets/post_cmake/Untitled.png)

### Add the cmake/bin to to your environment PATH

```bash
setx Path "%Path%;C:\Users\<username>\cmake\bin"
exit
```

# 2. Install MSYS2

---

[MSYS2](https://www.msys2.org/)

## Download the installer

[https://github.com/msys2/msys2-installer/releases/download/2023-10-26/msys2-x86_64-20231026.exe](https://github.com/msys2/msys2-installer/releases/download/2023-10-26/msys2-x86_64-20231026.exe)

### Follow the steps

![Untitled](/assets/post_cmake/Untitled%201.png)

![Untitled](/assets/post_cmake/Untitled%202.png)

Now MSYS2 is ready for you and a terminal for the [UCRT64 environment](https://www.msys2.org/docs/environments/) will launch

![Untitled](/assets/post_cmake/Untitled%203.png)

### Open MSYS2 MinGW64

![Untitled](/assets/post_cmake/Untitled%204.png)

### Install the mingw-w64 GCC

```bash
# type this in the msys2 mingw64 terminal
pacman -S mingw-w64-x86_64-toolchain
```

### Add path to the system variables

```bash
# type this in your own shell(cmd)
setx Path "%Path%;C:\msys64\mingw64\bin"
exit
```

### Check if the installations has done properly

```bash
# type this in your own shell(cmd)
mingw32-make --version
gcc --version
```

# 3. Create Project

### Open VS Code

![Untitled](/assets/post_cmake/Untitled%205.png)

### In your project, create CMakeLists.txt

![Untitled](/assets/post_cmake/Untitled%206.png)

```bash
cmake_minimum_required( VERSION 3.5 )
project( <your_project_name>)
add_executable( <your_project_name> <your_project_main.cpp> )
```

## Configure Make files for your project

### Open new terminal

![Untitled](/assets/post_cmake/Untitled%207.png)

![Untitled](/assets/post_cmake/Untitled%208.png)

```bash
# type this in your terminal
# should do this under your project folder, 
# e.g. PS <your_project_path>>
mkdir build
cd build
cmake -G "MinGW Makefiles" ..
```

### If configured successfully, the output should looks like this:

![Untitled](/assets/post_cmake/Untitled%209.png)

### Use make to build the C++ files

```bash
# type this under the project/build folder
mingw32-make
```

### If built successfully, the output should looks like this:

![Untitled](/assets/post_cmake/Untitled%2010.png)

### Then execute your program:

```bash
.\<your_project>.exe
```

![Untitled](/assets/post_cmake/Untitled%2011.png)

# 4. Compile OpenCV

---

[Releases](https://opencv.org/releases/)

### Click on the Sources then unzip (Recommend to unzip in C:/)

![Untitled](/assets/post_cmake/Untitled%2012.png)

## Build OpenCV locally

### Make directories

```bash
# in C:\opencv-4.8.0
# make files will be stored in mingw-build
mkdir mingw-build
# and compiled files will be stored here
mkdir build
cd mingw-build
# Create makefiles for mingw-make with cmake
cmake -G "MinGW Makefiles" C:\opencv-4.8.0 -DCMAKE_INSTALL_PREFIX=C:\opencv-4.8.0\build -DEBUILD_TESTS=OFF -DBUILD_PREF_TESTS=OFF
# Then compile (the parameter -j is to specify how many logic processors to run)
# I'm using i7-9700 which has 8 logic processors, so I used -j8 to make it faster
mingw32-make install -j8
# then set the path as environment variables
setx OpenCV_DIR " C:\opencv-4.8.0\build"
setx Path "%Path%: C:\opencv-4.8.0\build\x64\mingw\bin"
exit
```

### Configure OpenCV for your project

```bash
# in your CMakeLists.txt
cmake_minimum_required( VERSION 3.5 )
project( <project_name> )

find_package( OpenCV REQUIRED )
include_directories( ${OpenCV_INCLUDE_DIRS} )

add_executable( <project_name> <project_main.cpp> )
target_link_libraries( <project_name> ${OpenCV_LIBS})
```

### Include OpenCV header in your code

```C++
#include <opencv\opencv.hpp>
```