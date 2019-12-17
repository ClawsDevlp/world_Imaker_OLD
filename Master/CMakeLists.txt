#Réglages de base
cmake_minimum_required(VERSION 2.8)
project(world_Imaker)

add_definitions(-std=c++11)

set(CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/CMake)


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
endif()

# A décommenter sur machine de la fac
# set(OPENGL_LIBRARIES /usr/lib/x86_64-linux-gnu/libGL.so.1)

# ------------------------------ BUILD IF NEEDED ------------------------------

add_library(GLAD STATIC "lib/glad/src/glad.c")
add_library(STB_IMAGE STATIC "lib/stb_image/stb_image.cpp")
file(GLOB_RECURSE IMGUI_SOURCES lib/imgui/*)
add_library(IMGUI STATIC ${IMGUI_SOURCES})

# ///////  Inclusions /////////////////////////
include_directories(
    ${SDL2_INCLUDE_DIRS} 
    ${OPENGL_INCLUDE_DIR} 
    ${GLEW_INCLUDE_DIR} 
    lib/glimac/include
    lib/imgui 
    lib
    src
    )

# ////// Construction ////////////////////////

set(EXECUTABLE_OUTPUT_PATH ${CMAKE_BUILD_TYPE})
add_executable(${PROJECT_NAME} ${MY_COMMON} ${MY_SOURCES})

target_link_libraries(
    ALL_LIBRARIES glimac
    ${PROJECT_NAME}
    ${OPENGL_LIBRARIES}
    ${SDL2_LIBRARIES}
    ${GLEW_LIBRARY}
    IMGUI
)

add_subdirectory(glimac)

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