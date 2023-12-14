cmake_minimum_required(VERSION 3.14)

project(boost_json_archive LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_library(boost_json_archive SHARED
  src/json_iarchive.cpp
  src/json_oarchive.cpp
  src/picojson_wrapper.cpp
)

# Append debug suffix to debug library
set_target_properties(boost_json_archive PROPERTIES DEBUG_POSTFIX "d")

# This is required because cmake only searches for .a files but boost
# but we would like to search for .dll files
set(CMAKE_FIND_LIBRARY_SUFFIXES .dll ${CMAKE_FIND_LIBRARY_SUFFIXES})

find_package(Boost REQUIRED COMPONENTS serialization)

target_include_directories(boost_json_archive
	PUBLIC ${Boost_INCLUDE_DIRS}
	$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
	$<INSTALL_INTERFACE:include>)

target_link_libraries(boost_json_archive PRIVATE
	${Boost_SERIALIZATION_LIBRARY})

target_compile_definitions(boost_json_archive PRIVATE BOOST_JSON_ARCHIVE_LIBRARY)

# Install library
install(TARGETS boost_json_archive
	LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
	RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
)

# Install headers
install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/include/
	    DESTINATION include
		FILES_MATCHING PATTERN "*.h"
)
