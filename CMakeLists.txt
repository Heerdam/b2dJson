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

# ------------------ LIQUIDFUN ------------------
message(STATUS "Fetching LiquidFun...")
FetchContent_Declare(
		liquidfun
		GIT_REPOSITORY https://github.com/Heerdam/liquidfun.git
)
FetchContent_GetProperties(liquidfun)
if(NOT liquidfun_POPULATED)
	FetchContent_Populate(liquidfun)
	add_subdirectory("${liquidfun_SOURCE_DIR}" "${liquidfun_BINARY_DIR}")
endif()

target_link_libraries(b2djson PUBLIC
		rapidjson
		liquidfun	
)
