cmake_minimum_required(VERSION 3.11)

project(B2dJsonEx)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)
if (MSVC)
	set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} /O2 /DNDEBUG")
else()
	set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fno-rtti")
	set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -g -ggdb" )
	set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -O3 -march=native -DNDEBUG -flto")
endif()

include(FetchContent)

add_executable(b2djsonex
	src/private/main.cpp
)

add_subdirectory("${CMAKE_CURRENT_LIST_DIR}/.." b2djson)

# ------------------ BOX2D ------------------
message(STATUS "Fetching LiquidFun...")
FetchContent_Declare(
		Box2D
		GIT_REPOSITORY https://github.com/Heerdam/liquidfun.git
		SOURCE_SUBDIR liquidfun/Box2D
)
FetchContent_GetProperties(Box2D)
if(NOT box2d_POPULATED)

	option(BOX2D_INSTALL_DOC "Install Box2D documentation" OFF)
	option(BOX2D_BUILD_SHARED "Build Box2D shared libraries" OFF)
	option(BOX2D_BUILD_STATIC "Build Box2D static libraries" ON)
	option(BOX2D_BUILD_EXAMPLES "Build Box2D examples" OFF)
	option(BOX2D_BUILD_UNITTESTS "Build Box2D Unit Tests" OFF)
	# NOTE: Code coverage only works on Linux & OSX.
	option(BOX2D_CODE_COVERAGE "Enable the code coverage build option." OFF)

	FetchContent_Populate(Box2D)
	add_subdirectory("${box2d_SOURCE_DIR}/liquidfun/Box2D" "${box2d_BINARY_DIR}/liquidfun/Box2D")
endif()

target_link_libraries(b2djsonex
		b2djson
		Box2D	
)
