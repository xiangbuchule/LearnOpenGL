# cmake需要的最低版本
cmake_minimum_required (VERSION 3.12)
# 项目名
project(03HelloWindow)

# =====================================
# ========== 设置程序生成路径 =========
# =====================================
if(MSVC)
    set(TargetPath ${PROJECT_SOURCE_DIR}/bin)
else()
    set(TargetPath ${PROJECT_SOURCE_DIR}/bin/${CMAKE_BUILD_TYPE})
endif()
set(EXECUTABLE_OUTPUT_PATH ${TargetPath})

# =====================================
# =========== 使用的模块库 ============
# =====================================
# 设置glad库
set(GLAD glad)
set(GLADVersion 3.3)
# 设置glfw库
set(GLFW glfw)
set(GLFWVersion 3)

# =====================================
# ============ 设置头文件 =============
# =====================================
# 设置include文件（.h文件）的位置
set(IncludePath ${PROJECT_SOURCE_DIR}/include)
# 包含头文件路径
include_directories(
    ${PublicLibraryPath}/${GLAD}/${GLAD}${GLADVersion}/include
    ${PublicLibraryPath}/${GLFW}/include
    ${IncludePath}
)

# =====================================
# ============ 设置源文件 =============
# =====================================
# 设置源文件（.c/.cpp文件）的位置
set(SrcPath ${PROJECT_SOURCE_DIR}/src)
# 添加源文件
aux_source_directory(${SrcPath} SrcFiles)

# =====================================
# ========== 设置库文件位置 ===========
# =====================================
# 设置需要连接的库的路径
if(MSVC)
    link_directories(
        ${PublicSharedLibraryTargetPath}/${CMAKE_BUILD_TYPE}
        ${PublicStaticLibraryTargetPath}/${CMAKE_BUILD_TYPE}
    )
else()
    link_directories(
        ${PublicSharedLibraryTargetPath}
        ${PublicStaticLibraryTargetPath}
    )
endif()

# =====================================
# =========== 生成可执行文件 ==========
# =====================================
# 创建的可执行文件
add_executable(${PROJECT_NAME} ${SrcFiles})

# =====================================
# ============== 连接库 ===============
# =====================================
# 连接库
# target_link_libraries(${PROJECT_NAME} ${GLAD}${GLADVersion})
# 链接glfw库
if(BUILD_SHARED_LIBS AND UNIX)
    target_link_libraries(${PROJECT_NAME} ${GLAD}${GLADVersion} ${GLFW})
else()
    target_link_libraries(${PROJECT_NAME} ${GLAD}${GLADVersion} ${GLFW}${GLFWVersion})
endif()

# =====================================
# ==== 复制库文件到可执行文件路径 =====
# =====================================
# 如果glad、glfw是生成的动态库复制动态库到执行路径
if(BUILD_SHARED_LIBS)
    set(quotes "\"")
    if(WIN32)
        if(MSVC)
            STRING(REPLACE "/" "\\" replaceGLADSource ${PublicSharedLibraryTargetPath}/${CMAKE_BUILD_TYPE}/${GLAD}${GLADVersion}.dll)
            STRING(REPLACE "/" "\\" replaceGLFWSource ${PublicSharedLibraryTargetPath}/${CMAKE_BUILD_TYPE}/${GLFW}${GLFWVersion}.dll)
            add_custom_command(
                TARGET ${PROJECT_NAME}
                PRE_BUILD
                COMMAND copy ${quotes}${replaceGLADSource}${quotes} ${quotes}${TargetPath}/${CMAKE_BUILD_TYPE}${quotes}
                COMMAND copy ${quotes}${replaceGLFWSource}${quotes} ${quotes}${TargetPath}/${CMAKE_BUILD_TYPE}${quotes}
            )
        else()
            STRING(REPLACE "/" "\\" replaceGLADSource ${PublicSharedLibraryTargetPath}/${GLAD}${GLADVersion}.dll)
            STRING(REPLACE "/" "\\" replaceGLFWSource ${PublicSharedLibraryTargetPath}/${GLFW}${GLFWVersion}.dll)
            add_custom_command(
                TARGET ${PROJECT_NAME}
                PRE_BUILD
                COMMAND copy ${quotes}${replaceGLADSource}${quotes} ${quotes}${TargetPath}${quotes}
                COMMAND copy ${quotes}${replaceGLFWSource}${quotes} ${quotes}${TargetPath}${quotes}
            )
        endif()
    else()
        add_custom_command(
            TARGET ${PROJECT_NAME}
            PRE_BUILD
            COMMAND cp -p ${quotes}${PublicSharedLibraryTargetPath}/lib${GLAD}${GLADVersion}.so${quotes} ${quotes}${TargetPath}${quotes}
            COMMAND cp -p ${quotes}${PublicSharedLibraryTargetPath}/lib${GLFW}.so${quotes} ${quotes}${TargetPath}${quotes}
        )
    endif()
endif()

# =====================================
# ========== 安装生成的程序 ===========
# =====================================
# 设置安装的目录
set(InstallPath ${PROJECT_SOURCE_DIR}/install/${CMAKE_BUILD_TYPE})
# 安装
if(BUILD_SHARED_LIBS)
    install(
        TARGETS ${PROJECT_NAME} ${GLAD}${GLADVersion} ${GLFW}
        RUNTIME DESTINATION ${InstallPath}
        LIBRARY DESTINATION ${InstallPath}
    )
else()
    install(
        TARGETS ${PROJECT_NAME}
        RUNTIME DESTINATION ${InstallPath}
    )
endif()