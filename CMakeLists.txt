cmake_minimum_required(VERSION 3.22.1)
project("Stellar")

set(CMAKE_CXX_STANDARD 17)

add_compile_options(-Werror=format -fdata-sections -ffunction-sections -fno-exceptions -fno-rtti -fno-threadsafe-statics)
if (NOT CMAKE_BUILD_TYPE STREQUAL "Debug")
    add_compile_options(-Os -flto -fvisibility=hidden -fvisibility-inlines-hidden)
    add_link_options(-flto -Wl,--exclude-libs,ALL -Wl,--gc-sections -Wl,--strip-all)
endif ()

# 1. 主服务
add_executable(libstellar.so starter.cpp misc.cpp selinux.cpp cgroup.cpp)
target_link_libraries(libstellar.so log)

# 2. ADB 库 (注意：这个需要 boringssl，如果报错，咱们就把它去掉先不编)
add_library(adb SHARED adb_pairing.cpp misc.cpp)
target_link_libraries(adb log)

# 3. 用户身份切换
add_executable(libchid.so chid.cpp)
target_link_libraries(libchid.so log)

# 4. rish
add_library(rish SHARED rish.cpp)
target_link_libraries(rish log)
