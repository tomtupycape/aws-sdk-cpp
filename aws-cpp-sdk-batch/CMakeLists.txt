add_project(aws-cpp-sdk-batch "C++ SDK for the AWS batch service" aws-cpp-sdk-core)

file(GLOB AWS_BATCH_HEADERS
    "include/aws/batch/*.h"
)

file(GLOB AWS_BATCH_MODEL_HEADERS
    "include/aws/batch/model/*.h"
)

file(GLOB AWS_BATCH_SOURCE
    "source/*.cpp"
)

file(GLOB AWS_BATCH_MODEL_SOURCE
    "source/model/*.cpp"
)

file(GLOB BATCH_UNIFIED_HEADERS
    ${AWS_BATCH_HEADERS}
    ${AWS_BATCH_MODEL_HEADERS}
)

file(GLOB BATCH_UNITY_SRC
    ${AWS_BATCH_SOURCE}
    ${AWS_BATCH_MODEL_SOURCE}
)

if(ENABLE_UNITY_BUILD)
    enable_unity_build("BATCH" BATCH_UNITY_SRC)
endif()

file(GLOB BATCH_SRC
    ${BATCH_UNIFIED_HEADERS}
    ${BATCH_UNITY_SRC}
)

if(WIN32)
    #if we are compiling for visual studio, create a sane directory tree.
    if(MSVC)
        source_group("Header Files\\aws\\batch" FILES ${AWS_BATCH_HEADERS})
        source_group("Header Files\\aws\\batch\\model" FILES ${AWS_BATCH_MODEL_HEADERS})
        source_group("Source Files" FILES ${AWS_BATCH_SOURCE})
        source_group("Source Files\\model" FILES ${AWS_BATCH_MODEL_SOURCE})
    endif(MSVC)
endif()

set(BATCH_INCLUDES
    "${CMAKE_CURRENT_SOURCE_DIR}/include/"
)

add_library(${PROJECT_NAME} ${LIBTYPE} ${BATCH_SRC})
add_library(AWS::${PROJECT_NAME} ALIAS ${PROJECT_NAME})

set_compiler_flags(${PROJECT_NAME})
set_compiler_warnings(${PROJECT_NAME})

if(USE_WINDOWS_DLL_SEMANTICS AND BUILD_SHARED_LIBS)
    target_compile_definitions(${PROJECT_NAME} PRIVATE "AWS_BATCH_EXPORTS")
endif()

target_include_directories(${PROJECT_NAME} PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:include>)

target_link_libraries(${PROJECT_NAME} PRIVATE ${PLATFORM_DEP_LIBS} ${PROJECT_LIBS})
list(APPEND AWS_INSTALLED_LIBS "${PROJECT_NAME}")
set(AWS_INSTALLED_LIBS "${AWS_INSTALLED_LIBS}" PARENT_SCOPE)

setup_install()

install (FILES ${AWS_BATCH_HEADERS} DESTINATION ${INCLUDE_DIRECTORY}/aws/batch)
install (FILES ${AWS_BATCH_MODEL_HEADERS} DESTINATION ${INCLUDE_DIRECTORY}/aws/batch/model)

if(PLATFORM_WINDOWS AND MSVC)
    file(GLOB NUGET_FILES "nuget/${PROJECT_NAME}.*.autopkg")
    install (FILES ${NUGET_FILES} DESTINATION nuget)
endif()

do_packaging()


