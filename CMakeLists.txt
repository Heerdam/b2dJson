cmake_minimum_required(VERSION 3.11)

project(B2dJson)

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

add_library(b2djson STATIC
	src/public/b2dJson.h
	src/public/b2dJsonImage.h
	src/public/b2dJsonImage_OpenGL.h
	src/public/bitmap.h
	
	src/private/b2dJson.cpp
	src/private/b2dJsonImage.cpp
	src/private/b2dJsonImage_OpenGL.cpp
)

target_include_directories(b2djson PUBLIC ${CMAKE_CURRENT_LIST_DIR}/src/public)

# ------------------ RAPIDJSON ------------------
find_package (rapidjson 1.1.0 QUIET)
if(rapidjson_FOUND)
	message(STATUS "RapidJSON found!")
else()
	message(STATUS "RapidJSON not found! Fetching...")
	FetchContent_Declare(
			rapidjson
			GIT_REPOSITORY https://github.com/Tencent/rapidjson
	)
	FetchContent_GetProperties(rapidjson)
	if(NOT rapidjson_POPULATED)
	option(RAPIDJSON_BUILD_DOC "Build rapidjson documentation." OFF)
	option(RAPIDJSON_BUILD_EXAMPLES "Build rapidjson examples." OFF)
	option(RAPIDJSON_BUILD_TESTS "Build rapidjson perftests and unittests." OFF)

		FetchContent_Populate(rapidjson)
		add_subdirectory("${rapidjson_SOURCE_DIR}" "${rapidjson_BINARY_DIR}")
	endif()
endif ()

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

target_link_libraries(b2djson PUBLIC
		rapidjson
		Box2D	
)
