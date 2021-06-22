# cmake需要的最低版本
cmake_minimum_required (VERSION 3.12)
# 主项目名
project(Main)

# =====================================
# ============= 设置库类型 =============
# =====================================
if (POLICY CMP0077)
    cmake_policy(SET CMP0077 NEW)
endif()
# 设置默认构建动态库
option(BUILD_SHARED_LIBS "Build shared libraries" ON)

# =====================================
# =========== 设置c编译选项 ============
# =====================================
# 设置c编译版本
set(CMAKE_C_STANDARD 11)
# 设置c编译器
# set(CMAKE_C_COMPILER "gcc")
# message(${CMAKE_C_COMPILER})
# 设置c编译Debug与Release的编译参数
if(MSVC)
    set(CMAKE_C_FLAGS_DEBUG "/Od /Zi /MTd")
    set(CMAKE_C_FLAGS_RELEASE "/Ox /Wall /MT")
endif()
if(GNU)
    set(CMAKE_C_FLAGS_DEBUG "-O0 -g3 -ggdb3")
    set(CMAKE_C_FLAGS_RELEASE "-O3 -Wall")
endif()


# =====================================
# ========== 设置c++编译选项 ===========
# =====================================
# 设置c++编译版本
set(CMAKE_CXX_STANDARD 17)
# 设置c++编译器
# set(CMAKE_CXX_COMPILER "g++")
# message(${CMAKE_CXX_COMPILER})
# 设置c++编译Debug与Release的编译参数
if(MSVC)
    set(CMAKE_CXX_FLAGS_DEBUG "/Od /Zi /MTd")
    set(CMAKE_CXX_FLAGS_RELEASE "/Ox /Wall /MT")
endif()
if(GNU)
    set(CMAKE_CXX_FLAGS_DEBUG "-O0 -g3 -ggdb3")
    set(CMAKE_CXX_FLAGS_RELEASE "-O3 -Wall")
endif()

# =====================================
# ========= 设置额外编译选项 ===========
# =====================================
# 设置编码
set(CompileCode UTF-8)
if(MSVC)
    add_compile_options(/source-charset:${CompileCode} /execution-charset:${CompileCode})
endif()
if(GNU)
    add_compile_options(-finput-charset=${CompileCode} -fexec-charset=${CompileCode} -fwide-exec-charset=${CompileCode})
endif()

# =====================================
# === 设置编译版本是Debug还是Release ===
# =====================================
# 判断命令传入的是否为空，如果为空，默认Debug
if("${CMAKE_BUILD_TYPE}" STREQUAL "")
    set(CMAKE_BUILD_TYPE "Debug")
endif()

# =====================================
# ========== 设置公共库路径 ============
# =====================================
# 设置公共库源码所在目录
set(PublicLibraryPath ${PROJECT_SOURCE_DIR}/PublicLibrary)
# 设置共库生成目录
set(PublicLibraryTargetPath ${PROJECT_SOURCE_DIR}/Learn/MyPublicLibrary)
if(MSVC)
    set(PublicSharedLibraryTargetPath ${PROJECT_SOURCE_DIR}/Learn/MyPublicLibrary/Shared)
    set(PublicStaticLibraryTargetPath ${PROJECT_SOURCE_DIR}/Learn/MyPublicLibrary/Static)
else()
    set(PublicSharedLibraryTargetPath ${PROJECT_SOURCE_DIR}/Learn/MyPublicLibrary/Shared/${CMAKE_BUILD_TYPE})
    set(PublicStaticLibraryTargetPath ${PROJECT_SOURCE_DIR}/Learn/MyPublicLibrary/Static/${CMAKE_BUILD_TYPE})
endif()

# =====================================
# ============ 生成公共库 ==============
# =====================================
add_subdirectory(${PublicLibraryPath}/glfw)
add_subdirectory(${PublicLibraryPath}/glad/glad3.3)
add_subdirectory(${PublicLibraryPath}/glad/glad4.6)

# =====================================
# ============ 添加子项目 ==============
# =====================================
add_subdirectory(${PROJECT_SOURCE_DIR}/Learn)