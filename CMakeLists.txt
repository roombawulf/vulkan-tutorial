cmake_minimum_required(VERSION 3.16)
project(VulkanTutorial)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Find Vulkan
find_package(Vulkan REQUIRED)

# Find or specify GLFW for UCRT64
find_package(glfw3 CONFIG QUIET)
if(NOT glfw3_FOUND)
    # Manual GLFW setup for UCRT64
    find_library(GLFW_LIBRARIES
        NAMES glfw3 glfw
        PATHS 
            C:/msys64/ucrt64/lib
            ${CMAKE_PREFIX_PATH}/lib
        REQUIRED
    )
    set(GLFW_INCLUDE_DIRS 
        C:/msys64/ucrt64/include
        ${CMAKE_PREFIX_PATH}/include
    )
endif()

# Your executable
add_executable(${PROJECT_NAME} 
    VulkanTutorial/main.cpp
)

# Include directories
target_include_directories(${PROJECT_NAME} PRIVATE 
    ${Vulkan_INCLUDE_DIRS}
    ${GLFW_INCLUDE_DIRS}
)

# Link libraries
if(glfw3_FOUND)
    target_link_libraries(${PROJECT_NAME} 
        ${Vulkan_LIBRARIES}
        glfw
    )
else()
    target_link_libraries(${PROJECT_NAME} 
        ${Vulkan_LIBRARIES}
        ${GLFW_LIBRARIES}
    )
endif()

# Windows-specific settings
if(WIN32)
    target_compile_definitions(${PROJECT_NAME} PRIVATE 
        VK_USE_PLATFORM_WIN32_KHR
        NOMINMAX
        WIN32_LEAN_AND_MEAN
    )
    
    # Link Windows libraries
    target_link_libraries(${PROJECT_NAME} 
        user32 
        gdi32 
        shell32
    )
endif()

# Debug symbols for GDB
set(CMAKE_BUILD_TYPE Debug)
set(CMAKE_CXX_FLAGS_DEBUG "-g -O0")