#Réglages de base
cmake_minimum_required(VERSION 2.8)
project(world_Imaker)

add_definitions(-std=c++11)

set(CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/CMake)

# ///////// Fichiers du projet //////////////////
file(GLOB_RECURSE MY_SOURCES src/*.cpp )

# /////////  Eléments nécessaires  //////////////
find_package(OpenGL REQUIRED)
find_package(GLEW REQUIRED)

# Sur Windows
if (WIN32) 
    set(SDL2_INCLUDE_DIRS ${CMAKE_SOURCE_DIR}/lib/SDL2-2.0.10/include)
    set(SDL2_LIBRARIES ${CMAKE_SOURCE_DIR}/lib/SDL2-2.0.10/lib/x64/SDL2.lib;
                       ${CMAKE_SOURCE_DIR}/lib/SDL2-2.0.10/lib/x64/SDL2main.lib)

# Sur Linux
else()
    find_package(SDL2 REQUIRED)
    if(${SDL2_FOUND})
      message(STATUS "SDL2 found")
    endif()
endif()

# A décommenter sur machine de la fac
# set(OPENGL_LIBRARIES /usr/lib/x86_64-linux-gnu/libGL.so.1)

# ------------------------------ BUILD IF NEEDED ------------------------------

add_subdirectory(lib/glimac)
add_subdirectory(lib/glm)


# ///////  Inclusions /////////////////////////
include_directories(
    ${SDL2_INCLUDE_DIRS}
    ${OPENGL_INCLUDE_DIR} 
    ${GLEW_INCLUDE_DIRS}
    
    lib/glimac/include
    lib/third-party/include
    lib
    src
    )

# ////// Construction ////////////////////////

set(EXECUTABLE_OUTPUT_PATH ${CMAKE_BUILD_TYPE})
add_executable(${PROJECT_NAME} ${MY_SOURCES})

message(STATUS ${SDL2_INCLUDE_DIRS})

target_link_libraries(
    ${PROJECT_NAME}
    ${OPENGL_LIBRARIES}
    glimac
    ${SDL2_LIBRARIES}
    ${GLEW_LIBRARY}
    
)

#if (WIN32)
#  set(ALL_LIBRARIES 
#    stb_image
#    ${OPENGL_LIBRARIES}
#    ${SDL2_LIBRARIES}
#    ${GLEW_LIBRARY}
#    glimac
#    )
#else()
#  set(ALL_LIBRARIES 
#    stb_image
#    ${SDL2_LIBRARIES}
#    ${OPENGL_LIBRARIES}
#    ${GLEW_LIBRARY}
#    glimac
#    )

if (UNIX)
    target_link_libraries(${PROJECT_NAME} -ldl)
endif()

if (WIN32) # Copy .dll to build folder
    add_custom_command(
        TARGET ${PROJECT_NAME} POST_BUILD
        COMMAND ${CMAKE_COMMAND} -E copy_if_different
        "${PROJECT_SOURCE_DIR}/lib/SDL2-2.0.10/lib/x64/SDL2.dll"
        $<TARGET_FILE_DIR:${PROJECT_NAME}>
    )
endif()
