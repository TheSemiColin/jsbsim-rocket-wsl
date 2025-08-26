cmake_minimum_required(VERSION 3.12)
project(RocketSimulation)

# Set C++ standard
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# -----------------------------
# JSBSim library configuration
# -----------------------------

# Assume headers and libraries are in local include/ and lib/ folders
set(JSBSIM_INCLUDE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/include")
set(JSBSIM_LIBRARY_DIR "${CMAKE_CURRENT_SOURCE_DIR}/lib")

# Find JSBSim library (supports .so or .a)
find_library(JSBSIM_LIBRARY
    NAMES JSBSim
    PATHS "${JSBSIM_LIBRARY_DIR}"
)

# Fail if JSBSim is not found
if(NOT JSBSIM_LIBRARY)
    message(FATAL_ERROR "JSBSim library not found. Build JSBSim first.")
endif()

message(STATUS "JSBSim library: ${JSBSIM_LIBRARY}")
message(STATUS "JSBSim include dir: ${JSBSIM_INCLUDE_DIR}")

# -----------------------------
# Build rocket_sim executable
# -----------------------------
add_executable(rocket_sim rocket_sim.cpp)

# Include JSBSim headers
target_include_directories(rocket_sim PRIVATE ${JSBSIM_INCLUDE_DIR})

# Link JSBSim library
target_link_libraries(rocket_sim ${JSBSIM_LIBRARY})

# -----------------------------
# Copy aircraft configuration to build directory
# -----------------------------
file(COPY ${CMAKE_CURRENT_SOURCE_DIR}/aircraft
     DESTINATION ${CMAKE_CURRENT_BINARY_DIR})

# -----------------------------
# Runtime path for shared libraries (Linux)
# -----------------------------
if(UNIX AND NOT APPLE)
    set_target_properties(rocket_sim PROPERTIES
        INSTALL_RPATH_USE_LINK_PATH TRUE
    )
endif()
