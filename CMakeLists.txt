cmake_minimum_required(VERSION 3.22.1)
project("Stellar")

set(CMAKE_CXX_STANDARD 17)

add_compile_options(-Werror=format -fdata-sections -ffunction-sections -fno-exceptions -fno-rtti -fno-threadsafe-statics)
if (NOT CMAKE_BUILD_TYPE STREQUAL "Debug")
    add_compile_options(-Os -flto -fvisibility=hidden -fvisibility-inlines-hidden)
    # 去掉了坑爹的 -static，保留正常的优化和 strip
    add_link_options(-flto -Wl,--exclude-libs,ALL -Wl,--gc-sections -Wl,--strip-all)
endif ()

add_executable(libstellar.so starter.cpp misc.cpp selinux.cpp cgroup.cpp)
target_link_libraries(libstellar.so log)
