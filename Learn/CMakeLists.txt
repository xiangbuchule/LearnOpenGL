# cmake需要的最低版本
cmake_minimum_required (VERSION 3.12)
# 项目名
project(Learn)

# ==================================================
# =================== 添加子项目 ===================
# ==================================================
# 主项目不生成任何目标
# 子项目
add_subdirectory("${PROJECT_SOURCE_DIR}/01GettingStarted")