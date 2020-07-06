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
	src/b2dJson.h
	src/b2dJsonImage.h
	src/bitmap.h
	
	src/b2dJson.cpp
	src/b2dJsonImage.cpp
)

target_include_directories(b2djson PUBLIC ${CMAKE_CURRENT_LIST_DIR}/src)

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
		liquidfun	
)
