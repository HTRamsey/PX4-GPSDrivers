cmake_minimum_required(VERSION 3.10)

project(PX4-GPSDrivers CXX)

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_library(${PROJECT_NAME} "src/*")

target_include_directories(${PROJECT_NAME} PUBLIC src)

if(NOT DEFINED GPS_DEFINITIONS_HEADER OR NOT EXISTS "${GPS_DEFINITIONS_HEADER}")
	set(GPS_DEFINITIONS_HEADER "${CMAKE_CURRENT_SOURCE_DIR}/../definitions.h")
endif()
target_compile_definitions(${PROJECT_NAME} PUBLIC GPS_DEFINITIONS_HEADER)

include(CTest)
if(BUILD_TESTING)
	add_executable(gps-parser-test test/gps-parser-test.cpp)

	target_compile_options(gps-parser-test
		PRIVATE
			-Wall
			-Wextra
			-Wconversion
			-Wpedantic
	)

	target_include_directories(gps-parser-test PRIVATE test)

	target_link_libraries(gps-parser-test PRIVATE ${PROJECT_NAME})

	add_test(NAME GPSParserTest COMMAND gps-parser-test)
endif()