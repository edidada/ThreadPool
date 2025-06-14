cmake_minimum_required(VERSION 3.14)
project(ParallelRadixSort)

# 设置 C++ 标准
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall")

# 添加 pthread 支持（Linux/macOS）
if(UNIX AND NOT APPLE)
    find_package(Threads REQUIRED)
endif()

# 可选宏定义：通过 cmake -DDEBUG=ON 控制
option(DEBUG "Enable debug mode" OFF)
if(DEBUG)
    add_compile_definitions(DEBUG)
endif()

# 可选宏定义：RADIX_SORT_MIN_SIZE，用法：cmake -DRADIX_SORT_MIN_SIZE=100 ..
set(RADIX_SORT_MIN_SIZE "" CACHE STRING "Minimum size for radix sort")
if(RADIX_SORT_MIN_SIZE)
    add_compile_definitions(RADIX_SORT_MIN_SIZE=${RADIX_SORT_MIN_SIZE})
endif()

# 可选宏定义：TEST_OPTION_PARSER，用法：cmake -DTEST_OPTION_PARSER=ON ..
option(TEST_OPTION_PARSER "Enable test option parser" OFF)
if(TEST_OPTION_PARSER)
    add_compile_definitions(TEST_OPTION_PARSER)
endif()

# 源文件和头文件
set(SOURCES test.cpp)
set(HEADERS Options.h ParallelRadixSort.h ThreadPool.h)

# 目标可执行文件名
if(WIN32)
    set(TEST_EXECUTABLE test.exe)
else()
    set(TEST_EXECUTABLE test)
endif()

add_executable(${TEST_EXECUTABLE} ${SOURCES} ${HEADERS})

# 链接 pthread（Linux/macOS）
if(UNIX AND NOT APPLE)
    target_link_libraries(${TEST_EXECUTABLE} PRIVATE Threads::Threads)
endif()

# 定义 clean 和 help（虽然 CMake 不推荐自定义 clean，但可以模拟）
add_custom_target(cleantarget
    COMMAND ${CMAKE_COMMAND} -E remove ${TEST_EXECUTABLE}
    COMMENT "Cleaning build files"
)

add_custom_target(helptarget
    COMMAND ${CMAKE_COMMAND} -E echo "Build the project with optional defines:"
    COMMAND ${CMAKE_COMMAND} -E echo "  -DDEBUG=ON"
    COMMAND ${CMAKE_COMMAND} -E echo "  -DRADIX_SORT_MIN_SIZE=INT"
    COMMAND ${CMAKE_COMMAND} -E echo "  -DTEST_OPTION_PARSER=ON"
)