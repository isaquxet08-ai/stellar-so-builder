cmake_minimum_required(VERSION 3.22.1)
project("Stellar")

set(CMAKE_CXX_STANDARD 17)

add_compile_options(-Werror=format -fdata-sections -ffunction-sections -fno-exceptions -fno-rtti -fno-threadsafe-statics)
if (NOT CMAKE_BUILD_TYPE STREQUAL "Debug")
    add_compile_options(-Os -flto -fvisibility=hidden -fvisibility-inlines-hidden)
    add_link_options(-flto -Wl,--exclude-libs,ALL -Wl,--gc-sections -Wl,--strip-all)
endif ()

# 1. 核心服务
add_executable(libstellar.so starter.cpp misc.cpp selinux.cpp cgroup.cpp)
target_link_libraries(libstellar.so log)

# 2. 身份切换
add_executable(libchid.so chid.cpp)
target_link_libraries(libchid.so log)

# 3. ADB 库（指定 BoringSSL 的路径）
# 假设我们在 workflow 里把 boringssl 编到了外部目录
set(BORINGSSL_DIR "${CMAKE_SOURCE_DIR}/boringssl/build")
add_library(adb SHARED adb_pairing.cpp misc.cpp)
target_include_directories(adb PRIVATE "${CMAKE_SOURCE_DIR}/boringssl/include")
target_link_libraries(adb log ${BORINGSSL_DIR}/libcrypto.a ${BORINGSSL_DIR}/libssl.a)
