add_project(aws-cpp-sdk-organizations "C++ SDK for the AWS organizations service" aws-cpp-sdk-core)

file(GLOB AWS_ORGANIZATIONS_HEADERS
    "include/aws/organizations/*.h"
)

file(GLOB AWS_ORGANIZATIONS_MODEL_HEADERS
    "include/aws/organizations/model/*.h"
)

file(GLOB AWS_ORGANIZATIONS_SOURCE
    "source/*.cpp"
)

file(GLOB AWS_ORGANIZATIONS_MODEL_SOURCE
    "source/model/*.cpp"
)

file(GLOB ORGANIZATIONS_UNIFIED_HEADERS
    ${AWS_ORGANIZATIONS_HEADERS}
    ${AWS_ORGANIZATIONS_MODEL_HEADERS}
)

file(GLOB ORGANIZATIONS_UNITY_SRC
    ${AWS_ORGANIZATIONS_SOURCE}
    ${AWS_ORGANIZATIONS_MODEL_SOURCE}
)

if(ENABLE_UNITY_BUILD)
    enable_unity_build("ORGANIZATIONS" ORGANIZATIONS_UNITY_SRC)
endif()

file(GLOB ORGANIZATIONS_SRC
    ${ORGANIZATIONS_UNIFIED_HEADERS}
    ${ORGANIZATIONS_UNITY_SRC}
)

if(WIN32)
    #if we are compiling for visual studio, create a sane directory tree.
    if(MSVC)
        source_group("Header Files\\aws\\organizations" FILES ${AWS_ORGANIZATIONS_HEADERS})
        source_group("Header Files\\aws\\organizations\\model" FILES ${AWS_ORGANIZATIONS_MODEL_HEADERS})
        source_group("Source Files" FILES ${AWS_ORGANIZATIONS_SOURCE})
        source_group("Source Files\\model" FILES ${AWS_ORGANIZATIONS_MODEL_SOURCE})
    endif(MSVC)
endif()

set(ORGANIZATIONS_INCLUDES
    "${CMAKE_CURRENT_SOURCE_DIR}/include/"
)

add_library(${PROJECT_NAME} ${LIBTYPE} ${ORGANIZATIONS_SRC})
add_library(AWS::${PROJECT_NAME} ALIAS ${PROJECT_NAME})

set_compiler_flags(${PROJECT_NAME})
set_compiler_warnings(${PROJECT_NAME})

if(USE_WINDOWS_DLL_SEMANTICS AND BUILD_SHARED_LIBS)
    target_compile_definitions(${PROJECT_NAME} PRIVATE "AWS_ORGANIZATIONS_EXPORTS")
endif()

target_include_directories(${PROJECT_NAME} PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:include>)

target_link_libraries(${PROJECT_NAME} PRIVATE ${PLATFORM_DEP_LIBS} ${PROJECT_LIBS})
list(APPEND AWS_INSTALLED_LIBS "${PROJECT_NAME}")
set(AWS_INSTALLED_LIBS "${AWS_INSTALLED_LIBS}" PARENT_SCOPE)

setup_install()

install (FILES ${AWS_ORGANIZATIONS_HEADERS} DESTINATION ${INCLUDE_DIRECTORY}/aws/organizations)
install (FILES ${AWS_ORGANIZATIONS_MODEL_HEADERS} DESTINATION ${INCLUDE_DIRECTORY}/aws/organizations/model)

if(PLATFORM_WINDOWS AND MSVC)
    file(GLOB NUGET_FILES "nuget/${PROJECT_NAME}.*.autopkg")
    install (FILES ${NUGET_FILES} DESTINATION nuget)
endif()

do_packaging()


