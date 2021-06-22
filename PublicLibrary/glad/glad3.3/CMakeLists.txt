# cmake需要的最低版本
cmake_minimum_required (VERSION 3.12)
# 项目名
project(glad3.3)

# =====================================
# ============= 设置库类型 =============
# =====================================
if (POLICY CMP0077)
    cmake_policy(SET CMP0077 NEW)
endif()
# 设置默认构建动态库
option(BUILD_SHARED_LIBS "Build shared libraries" ON)

# =====================================
# ============= 设置头文件 =============
# =====================================
# 设置include文件（.h文件）的位置
set(IncludePath ${PROJECT_SOURCE_DIR}/include)
# 包含头文件路径
include_directories(
    ${IncludePath}
)

# =====================================
# ============= 设置源文件 =============
# =====================================
# 设置源文件（.c/.cpp文件）的位置
set(SrcPath ${PROJECT_SOURCE_DIR}/src)
# 添加源文件
aux_source_directory(${SrcPath} SrcFiles)

# =====================================
# ============= 生成库文件 =============
# =====================================
# 生成glad的库
if(BUILD_SHARED_LIBS)
    # 设置glad库文件的输出名称，目录
    set(LIBRARY_OUTPUT_PATH ${PublicSharedLibraryTargetPath})
    add_library(${PROJECT_NAME} SHARED ${SrcFiles})
    if(MSVC)
        # 设置生成动态库时，生成的需要导入lib文件名的后缀
        set_target_properties(${PROJECT_NAME} PROPERTIES IMPORT_SUFFIX ".lib")
        # 添加宏，设置导处dll时也导出对应的lib文件
        target_compile_definitions(${PROJECT_NAME}
            PRIVATE GLAD_GLAPI_EXPORT
            PRIVATE GLAD_GLAPI_EXPORT_BUILD
        )
    else()
        set_target_properties(${PROJECT_NAME} PROPERTIES IMPORT_SUFFIX ".a")
    endif()
else()
    # 设置glad库文件的输出名称，目录
    set(LIBRARY_OUTPUT_PATH ${PublicStaticLibraryTargetPath})
    add_library(${PROJECT_NAME} STATIC ${SrcFiles})
endif()
# 设置生成的库名不含开头的lib
if(WIN32)
    set_target_properties(${PROJECT_NAME} PROPERTIES PREFIX "")
endif()