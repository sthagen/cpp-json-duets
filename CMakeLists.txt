cmake_minimum_required(VERSION 3.7...3.23)
if(${CMAKE_VERSION} VERSION_LESS 3.12)
    cmake_policy(VERSION ${CMAKE_MAJOR_VERSION}.${CMAKE_MINOR_VERSION})
endif()

# ---- Project ----

project(cpp-json-duets LANGUAGES CXX)

# ---- Enable FetchContent ----

include(FetchContent)

# ---- Add dependencies via FetchContent ----

# ---- Fetch CPM ----
set(CPM_DOWNLOAD_VERSION 0.35.0)

if(CPM_SOURCE_CACHE)
    set(CPM_DOWNLOAD_LOCATION "${CPM_SOURCE_CACHE}/cpm/CPM_${CPM_DOWNLOAD_VERSION}.cmake")
elseif(DEFINED ENV{CPM_SOURCE_CACHE})
    set(CPM_DOWNLOAD_LOCATION "$ENV{CPM_SOURCE_CACHE}/cpm/CPM_${CPM_DOWNLOAD_VERSION}.cmake")
else()
    set(CPM_DOWNLOAD_LOCATION "${CMAKE_BINARY_DIR}/cmake/CPM_${CPM_DOWNLOAD_VERSION}.cmake")
endif()

if(NOT (EXISTS ${CPM_DOWNLOAD_LOCATION}))
    message(STATUS "Downloading CPM.cmake to ${CPM_DOWNLOAD_LOCATION}")
    file(
            DOWNLOAD
            https://github.com/cpm-cmake/CPM.cmake/releases/download/v${CPM_DOWNLOAD_VERSION}/CPM.cmake
            ${CPM_DOWNLOAD_LOCATION}
    )
endif()

include(${CPM_DOWNLOAD_LOCATION})

# ---- Add dependencies via CPM ----
# See https://github.com/cpm-cmake/CPM.cmake for details and examples

CPMAddPackage(
        NAME doctest
        GITHUB_REPOSITORY sthagen/doctest-doctest
        GIT_TAG 2020.12
)

CPMAddPackage(
        NAME cxxopts
        GITHUB_REPOSITORY jarro2783/cxxopts
        VERSION 3.0.0
        OPTIONS
        "CXXOPTS_BUILD_EXAMPLES Off"
        "CXXOPTS_BUILD_TESTS Off"
)

CPMAddPackage(
        NAME nlohmann_json
        VERSION 3.10.5
        # not using the repo as it takes forever to clone
        URL https://github.com/nlohmann/json/releases/download/v3.10.5/include.zip
        URL_HASH SHA256=b94997df68856753b72f0d7a3703b7d484d4745c567f3584ef97c96c25a5798e
)

if (nlohmann_json_ADDED)
        add_library(nlohmann_json INTERFACE)
        target_include_directories(nlohmann_json INTERFACE ${nlohmann_json_SOURCE_DIR})
endif ()

CPMAddPackage(
        NAME spdlog
        GITHUB_REPOSITORY sthagen/spdlog
        GIT_TAG 2022.3.24
)

