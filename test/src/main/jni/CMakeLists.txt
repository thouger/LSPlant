cmake_minimum_required(VERSION 3.18.1)
project("lsplant_test")

set(CMAKE_CXX_STANDARD 23)

# 添加子目录
add_subdirectory(external/lsparself)

# 创建主库
add_library(test SHARED test.cpp)

# 设置包含路径
target_include_directories(test
        PRIVATE
        ${CMAKE_CURRENT_SOURCE_DIR}/external/lsparself  # 添加 lsparself 的头文件路径
)

# 设置模块扫描
set_target_properties(test PROPERTIES CXX_SCAN_FOR_MODULES ON)

# 查找依赖包
find_package(dobby REQUIRED CONFIG)
find_package(lsplant REQUIRED CONFIG)

# LSPlant 模块设置
get_property(lsplant_INCLUDE TARGET lsplant::lsplant PROPERTY INTERFACE_INCLUDE_DIRECTORIES)
file(GLOB_RECURSE lsplant_MODULES "${lsplant_INCLUDE}/*.ixx")
target_sources(lsplant::lsplant INTERFACE FILE_SET CXX_MODULES BASE_DIRS ${lsplant_INCLUDE} FILES ${lsplant_MODULES})
set_target_properties(lsplant::lsplant PROPERTIES IMPORTED_CXX_MODULES_COMPILE_FEATURES "cxx_std_23")
set_target_properties(lsplant::lsplant PROPERTIES IMPORTED_CXX_MODULES_INCLUDE_DIRECTORIES "${lsplant_INCLUDE}")

# 链接库
target_link_libraries(test
        PRIVATE
        log
        dobby::dobby
        lsplant::lsplant
        lsparself
)