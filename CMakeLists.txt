cmake_minimum_required(VERSION 3.16)
project(LearnOpenGL LANGUAGES C CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
set(CMAKE_BUILD_TYPE Debug)

# ---------- GLAD：本地源码，两平台共用 ----------
add_library(glad STATIC glad/src/gl.c)
target_include_directories(glad PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/glad/include)

# ---------- 可执行文件 ----------
add_executable(${PROJECT_NAME} main.cpp)
target_link_libraries(${PROJECT_NAME} PRIVATE glad)

# ---------- GLFW：区分平台 ----------
if (WIN32)
    # Windows：vcpkg
    find_package(glfw3 CONFIG REQUIRED)
    target_link_libraries(${PROJECT_NAME} PRIVATE glfw opengl32)
else()
    # Linux：Arch 等发行版的 pkg-config
    find_package(PkgConfig REQUIRED)
    pkg_check_modules(GLFW REQUIRED IMPORTED_TARGET glfw3)
    target_link_libraries(${PROJECT_NAME} PRIVATE PkgConfig::GLFW)

    find_package(OpenGL REQUIRED)
    target_link_libraries(${PROJECT_NAME} PRIVATE OpenGL::GL ${CMAKE_DL_LIBS})
endif()
