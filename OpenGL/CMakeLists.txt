cmake_minimum_required(VERSION 3.10)
cmake_policy(SET CMP0074 NEW)

project(ACG_HW1 CXX)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED True)

find_package(OpenGL REQUIRED)
if(OPENGL_FOUND)
  message(STATUS "OpenGL include dir: ${OPENGL_INCLUDE_DIR}")
  message(STATUS "OpenGL libraries: ${OPENGL_LIBRARIES}")
else()
  message(FATAL_ERROR "OpenGL not found!")
endif()

set(GLUT_ROOT_PATH "D:/lib/freeglut-3.0.0")   # Set this for your computer !!!
find_package(GLUT REQUIRED)
if(GLUT_FOUND)
  message(STATUS "GLUT include dir: ${GLUT_INCLUDE_DIR}")
  message(STATUS "GLUT libraries: ${GLUT_LIBRARIES}")
else()
  message(FATAL_ERROR "GLUT not found!")
endif()

find_package(GLEW REQUIRED)
if(GLEW_FOUND)
  message(STATUS "GLEW include dir: ${GLEW_INCLUDE_DIRS}")
  message(STATUS "GLEW libraries: ${GLEW_LIBRARIES}")
else()
  message(FATAL_ERROR "GLEW not found!")
endif()

set(Angel_HDRS
  square/vec.h
  square/mat.h
  square/Angel.h
)
set(Angel_SRCS
  square/InitShader.cpp
)
add_library(Angel STATIC
  ${Angel_HDRS}
  ${Angel_SRCS}
)
target_include_directories(Angel PUBLIC
  square/
)
target_include_directories(Angel PUBLIC
  ${OPENGL_INCLUDE_DIR}
  ${GLUT_INCLUDE_DIR}
  ${GLEW_INCLUDE_DIRS}
)
target_link_libraries(Angel PUBLIC
  ${OPENGL_LIBRARIES}
  ${GLUT_LIBRARIES}
  ${GLEW_LIBRARIES}
)

add_executable(ACG-HW1 
  square/square.cpp
)
target_link_libraries(ACG-HW1 PUBLIC Angel)
