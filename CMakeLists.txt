cmake_minimum_required(VERSION 3.16.3)

project(CMakeTesting)

add_executable(${PROJECT_NAME} main.cpp)

add_subdirectory(Adder)

add_subdirectory(external/glfw)

# Downloads Submodules
find_package(Git QUIET)
if(GIT_FOUND AND EXISTS "S{PROJECT_SOURCE_DIR}/.git")

# Update submodules as needed
    option(GIT_SUBMODULE "Check submodules during build" ON)
    if(GIT_SUBMODULE)
        message(STATUS "Submodule update")
        execute_process(COMMAND ${GIT_EXECUTABLE}
                        submodule update --init --recursive
                        WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
                        RESULT_VARIABLE GIT_SUBMOD_RESULT)
        
        if(NOT GIT_SUBMOD_RESULT EQUAL "0")
            message(FATAL_ERROR "git submodule update --init failed with ${GIT_SUBMOD_RESULT}, please checkout submodules")
        endif()
    endif()
endif()

# Check each submodules
if(NOT EXISTS "${PROJECT_SOURCE_DIR}/external/glfw/CMakeLists.txt")
    message(FATAL_ERROR "The glfw submodule was not downloaded! GIT_SUBMODULE was turned off or failed. Please update submodule glfw")
endif()



target_include_directories(${PROJECT_NAME}

    PUBLIC Adder
    PUBLIC externel/glfw/include
)

target_link_directories(${PROJECT_NAME}

    PRIVATE Adder
    PRIVATE externel/glfw/src
)

target_link_libraries(${PROJECT_NAME}
    adder
    glfw
)
