cmake_minimum_required(VERSION 3.1)
project(libshell)

set (CMAKE_CXX_STANDARD 11)
set(MSVC_RUNTIME "dynamic")
set(LIBIGL_EIGEN_VERSION 3.3.7 CACHE STRING "Eigen version")

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CMAKE_CURRENT_SOURCE_DIR}/cmake)

# libigl
option(LIBIGL_OPENGL            "Use OpenGL"         ON)
option(LIBIGL_GLFW       		"Use GLFW"           ON)
option(LIBIGL_IMGUI				"Use ImGui"          ON)
include(libigl)

# Add your project files
if(MSVC)
	add_definitions(-DEIGEN_STRONG_INLINE=inline)
endif()

file(GLOB LIBFILES src/*.cpp src/SecondFundamentalForm/*.cpp src/MaterialModel/*.cpp include/*.h)
add_library(${PROJECT_NAME} STATIC ${LIBFILES})
if(LIBIGL_FOUND)
	target_link_libraries(${PROJECT_NAME} igl::core)
else()
	target_include_directories(${PROJECT_NAME} PRIVATE "$ENV{EIGEN3_INCLUDE_DIR}" )
endif()
install(TARGETS ${PROJECT_NAME} DESTINATION ${CMAKE_CURRENT_LIST_DIR}/lib)

file(GLOB EXAMPLEFILES example/*.cpp example/*.h)
add_executable(example_${PROJECT_NAME} ${EXAMPLEFILES})
target_link_libraries(example_${PROJECT_NAME} ${PROJECT_NAME} igl::core igl::glfw igl::imgui)

file(GLOB TESTFILES tests/*.cpp tests/*.h)
add_executable(tests_${PROJECT_NAME} ${TESTFILES})
target_link_libraries(tests_${PROJECT_NAME} ${PROJECT_NAME} igl::core)
