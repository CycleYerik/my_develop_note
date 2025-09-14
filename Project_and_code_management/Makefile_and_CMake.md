# Makefile and CMake

- 记录用CMake构建构成的大致过程

## CMake 基础概念

CMake是一个跨平台的构建系统生成器，它通过`CMakeLists.txt`文件描述项目结构，然后生成相应平台的构建文件（如Makefile、Visual Studio项目等）。

### 核心概念
- **Source Directory**: 包含源代码和CMakeLists.txt的目录
- **Build Directory**: 构建输出目录（推荐与源码分离）
- **Target**: 构建目标（可执行文件、库等）
- **Generator**: 构建系统生成器（Unix Makefiles、Ninja等）

## Basic Commands

### 命令行基本用法
```bash
# 生成项目构建系统
cmake [<options>] <path-to-source>
cmake [<options>] <path-to-existing-build>
cmake [<options>] -S <path-to-source> -B <path-to-build>

# 构建项目
cmake --build <dir> [<options>] [-- <build-tool-options>]

# 安装项目
cmake --install <dir> [<options>]

# 打开项目
cmake --open <dir>

# 运行脚本
cmake [{-D <var>=<value>}...] -P <cmake-script-file>

# 运行命令行工具
cmake -E <command> [<options>]

# 查找包工具
cmake --find-package [<options>]

# 查看帮助
cmake --help[-<topic>]
```

### 常用构建流程
```bash
# 1. 创建构建目录
mkdir build && cd build

# 2. 配置项目 (Debug模式)
cmake -DCMAKE_BUILD_TYPE=Debug ..

# 3. 构建项目
cmake --build .

# 4. 运行测试 (可选)
ctest

# 5. 安装 (可选)
cmake --install . --prefix /usr/local
```

## CMakeLists.txt 基础结构

### 1. 最简单的项目
```cmake
# 指定CMake最低版本
cmake_minimum_required(VERSION 3.10)

# 项目名称和版本
project(MyProject VERSION 1.0)

# 设置C++标准
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# 添加可执行文件
add_executable(my_app main.cpp)
```

### 2. 包含多个源文件的项目
```cmake
cmake_minimum_required(VERSION 3.10)
project(MyProject VERSION 1.0)

set(CMAKE_CXX_STANDARD 17)

# 收集所有源文件
file(GLOB_RECURSE SOURCES 
    "src/*.cpp"
    "src/*.c"
)

# 收集头文件
file(GLOB_RECURSE HEADERS 
    "include/*.h"
    "include/*.hpp"
)

# 添加头文件目录
include_directories(include)

# 创建可执行文件
add_executable(${PROJECT_NAME} ${SOURCES} ${HEADERS})
```

## 库的创建和链接

### 1. 创建静态库
```cmake
cmake_minimum_required(VERSION 3.10)
project(MathLib)

# 创建静态库
add_library(math_static STATIC
    src/math_utils.cpp
    src/calculator.cpp
)

# 设置头文件目录
target_include_directories(math_static PUBLIC include)

# 主程序
add_executable(main main.cpp)

# 链接库
target_link_libraries(main math_static)
```

### 2. 创建动态库
```cmake
# 创建动态库
add_library(math_shared SHARED
    src/math_utils.cpp
    src/calculator.cpp
)

# 设置库的属性
set_target_properties(math_shared PROPERTIES
    VERSION ${PROJECT_VERSION}
    SOVERSION 1
    PUBLIC_HEADER include/math_utils.h
)
```

## 复杂项目结构示例

### 项目目录结构
```
MyProject/
├── CMakeLists.txt          # 主CMake文件
├── src/                    # 源代码目录
│   ├── CMakeLists.txt
│   ├── main.cpp
│   └── utils/
│       ├── CMakeLists.txt
│       ├── string_utils.cpp
│       └── file_utils.cpp
├── include/                # 头文件目录
│   └── utils/
│       ├── string_utils.h
│       └── file_utils.h
├── tests/                  # 测试目录
│   ├── CMakeLists.txt
│   └── test_main.cpp
├── external/               # 第三方库
└── docs/                   # 文档
```

### 主CMakeLists.txt
```cmake
cmake_minimum_required(VERSION 3.10)
project(MyProject VERSION 1.2.0)

# 设置编译选项
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# 设置编译类型
if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Release)
endif()

# 编译选项
set(CMAKE_CXX_FLAGS "-Wall -Wextra")
set(CMAKE_CXX_FLAGS_DEBUG "-g")
set(CMAKE_CXX_FLAGS_RELEASE "-O3")

# 添加头文件搜索路径
include_directories(include)

# 添加子目录
add_subdirectory(src)
add_subdirectory(tests)

# 启用测试
enable_testing()
```

### src/CMakeLists.txt
```cmake
# 添加工具库子目录
add_subdirectory(utils)

# 主程序源文件
add_executable(${PROJECT_NAME} main.cpp)

# 链接工具库
target_link_libraries(${PROJECT_NAME} utils_lib)

# 设置可执行文件输出目录
set_target_properties(${PROJECT_NAME} PROPERTIES
    RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin
)
```

### src/utils/CMakeLists.txt
```cmake
# 工具库源文件
set(UTILS_SOURCES
    string_utils.cpp
    file_utils.cpp
)

# 创建静态库
add_library(utils_lib STATIC ${UTILS_SOURCES})

# 设置头文件路径
target_include_directories(utils_lib PUBLIC 
    ${CMAKE_SOURCE_DIR}/include
)
```

## 第三方库集成

### 1. 使用find_package
```cmake
# 查找线程库
find_package(Threads REQUIRED)
target_link_libraries(my_app Threads::Threads)

# 查找Boost库
find_package(Boost REQUIRED COMPONENTS system filesystem)
target_link_libraries(my_app ${Boost_LIBRARIES})
target_include_directories(my_app PRIVATE ${Boost_INCLUDE_DIRS})

# 查找OpenCV
find_package(OpenCV REQUIRED)
target_link_libraries(my_app ${OpenCV_LIBS})
```

### 2. 使用FetchContent (CMake 3.11+)
```cmake
include(FetchContent)

# 获取GoogleTest
FetchContent_Declare(
    googletest
    URL https://github.com/google/googletest/archive/03597a01ee50f33f9142fd2d6828d8d8abe6a0e8.zip
)

FetchContent_MakeAvailable(googletest)

# 链接到测试程序
target_link_libraries(my_test gtest_main)
```

### 3. 使用ExternalProject
```cmake
include(ExternalProject)

ExternalProject_Add(
    json
    URL https://github.com/nlohmann/json/releases/download/v3.11.2/json.tar.xz
    CMAKE_ARGS -DCMAKE_INSTALL_PREFIX=${CMAKE_BINARY_DIR}/external
)

add_dependencies(my_app json)
```

## 测试集成

### tests/CMakeLists.txt
```cmake
# 查找GoogleTest
find_package(GTest REQUIRED)

# 测试源文件
set(TEST_SOURCES
    test_main.cpp
    test_string_utils.cpp
    test_file_utils.cpp
)

# 创建测试可执行文件
add_executable(unit_tests ${TEST_SOURCES})

# 链接库
target_link_libraries(unit_tests 
    utils_lib
    GTest::GTest 
    GTest::Main
)

# 添加测试
add_test(NAME unit_tests COMMAND unit_tests)
```

## 常用变量和函数

### 重要的内置变量
```cmake
# 项目相关
${PROJECT_NAME}              # 项目名称
${PROJECT_VERSION}           # 项目版本
${CMAKE_SOURCE_DIR}          # 源码根目录
${CMAKE_BINARY_DIR}          # 构建根目录
${CMAKE_CURRENT_SOURCE_DIR}  # 当前CMakeLists.txt所在目录

# 系统相关
${CMAKE_SYSTEM_NAME}         # 系统名称 (Linux, Windows, Darwin)
${CMAKE_SYSTEM_PROCESSOR}    # 处理器架构
${CMAKE_BUILD_TYPE}          # 构建类型 (Debug, Release)

# 编译器相关
${CMAKE_C_COMPILER}          # C编译器
${CMAKE_CXX_COMPILER}        # C++编译器
${CMAKE_CXX_STANDARD}        # C++标准
```

### 常用函数
```cmake
# 设置变量
set(VAR_NAME "value")

# 添加编译定义
add_definitions(-DDEBUG)

# 条件判断
if(CMAKE_BUILD_TYPE STREQUAL "Debug")
    message("Debug mode enabled")
endif()

# 循环
foreach(src ${SOURCES})
    message("Source file: ${src}")
endforeach()

# 函数定义
function(my_function arg1 arg2)
    message("Arguments: ${arg1}, ${arg2}")
endfunction()
```

## 跨平台配置

### 平台特定设置
```cmake
# Windows特定设置
if(WIN32)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /W4")
    add_definitions(-D_CRT_SECURE_NO_WARNINGS)
endif()

# Linux特定设置
if(UNIX AND NOT APPLE)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -pthread")
    find_package(PkgConfig REQUIRED)
endif()

# macOS特定设置
if(APPLE)
    set(CMAKE_MACOSX_RPATH ON)
    set(CMAKE_OSX_DEPLOYMENT_TARGET "10.15")
endif()
```

## 实用技巧

### 1. 输出调试信息
```cmake
message(STATUS "Build type: ${CMAKE_BUILD_TYPE}")
message(WARNING "This is a warning")
message(FATAL_ERROR "This will stop configuration")
```

### 2. 选项和缓存变量
```cmake
# 定义选项
option(BUILD_TESTS "Build test programs" ON)
option(BUILD_SHARED_LIBS "Build shared libraries" OFF)

# 缓存变量
set(CUSTOM_PATH "/usr/local" CACHE PATH "Custom installation path")

# 使用选项
if(BUILD_TESTS)
    add_subdirectory(tests)
endif()
```

### 3. 生成器表达式
```cmake
# 根据配置类型设置不同的编译选项
target_compile_options(my_app PRIVATE
    $<$<CONFIG:Debug>:-g -O0>
    $<$<CONFIG:Release>:-O3 -DNDEBUG>
)

# 根据编译器设置选项
target_compile_options(my_app PRIVATE
    $<$<CXX_COMPILER_ID:GNU>:-Wall -Wextra>
    $<$<CXX_COMPILER_ID:MSVC>:/W4>
)
```

## 最佳实践

1. **使用现代CMake语法** (target_* 函数优于全局设置)
2. **分离源码和构建目录** (out-of-source build)
3. **合理组织项目结构** (使用add_subdirectory)
4. **版本控制** (指定最低CMake版本)
5. **跨平台兼容性** (测试不同平台)
6. **使用缓存和选项** (提供配置灵活性)

## 常见问题解决

### 1. 找不到头文件
```cmake
# 添加头文件搜索路径
target_include_directories(my_target PRIVATE include)
target_include_directories(my_target PUBLIC include)  # 传递给依赖者
```

### 2. 链接错误
```cmake
# 检查库的链接顺序
target_link_libraries(my_app lib1 lib2)  # lib1依赖lib2

# 使用完整路径
target_link_libraries(my_app /path/to/libname.a)
```

### 3. 清理构建
```bash
# 清理构建文件
cmake --build . --target clean

# 或者直接删除构建目录
rm -rf build/
```
