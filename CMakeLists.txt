cmake_minimum_required(VERSION 3.22.1)
project("Stellar")

set(CMAKE_CXX_STANDARD 17)

# 强制静态链接 C++ 标准库，防止手机里缺库
add_compile_options(-Werror=format -fdata-sections -ffunction-sections -fno-exceptions -fno-rtti -fno-threadsafe-statics)
if (NOT CMAKE_BUILD_TYPE STREQUAL "Debug")
    add_compile_options(-Os -flto -fvisibility=hidden -fvisibility-inlines-hidden)
    add_link_options(-flto -Wl,--exclude-libs,ALL -Wl,--gc-sections -Wl,--strip-all -static-libstdc++ -static)
endif ()

add_executable(libstellar.so starter.cpp misc.cpp selinux.cpp cgroup.cpp)
target_link_libraries(libstellar.so log)
