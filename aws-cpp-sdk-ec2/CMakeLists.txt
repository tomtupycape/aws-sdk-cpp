add_project(aws-cpp-sdk-ec2 "C++ SDK for the AWS ec2 service" aws-cpp-sdk-core)

file(GLOB AWS_EC2_HEADERS
    "include/aws/ec2/*.h"
)

file(GLOB AWS_EC2_MODEL_HEADERS
    "include/aws/ec2/model/*.h"
)

file(GLOB AWS_EC2_SOURCE
    "source/*.cpp"
)

file(GLOB AWS_EC2_MODEL_SOURCE
    "source/model/*.cpp"
)

file(GLOB EC2_UNIFIED_HEADERS
    ${AWS_EC2_HEADERS}
    ${AWS_EC2_MODEL_HEADERS}
)

file(GLOB EC2_UNITY_SRC
    ${AWS_EC2_SOURCE}
    ${AWS_EC2_MODEL_SOURCE}
)

if(ENABLE_UNITY_BUILD)
    enable_unity_build("EC2" EC2_UNITY_SRC)
endif()

file(GLOB EC2_SRC
    ${EC2_UNIFIED_HEADERS}
    ${EC2_UNITY_SRC}
)

if(WIN32)
    #if we are compiling for visual studio, create a sane directory tree.
    if(MSVC)
        source_group("Header Files\\aws\\ec2" FILES ${AWS_EC2_HEADERS})
        source_group("Header Files\\aws\\ec2\\model" FILES ${AWS_EC2_MODEL_HEADERS})
        source_group("Source Files" FILES ${AWS_EC2_SOURCE})
        source_group("Source Files\\model" FILES ${AWS_EC2_MODEL_SOURCE})
    endif(MSVC)
endif()

set(EC2_INCLUDES
    "${CMAKE_CURRENT_SOURCE_DIR}/include/"
)

add_library(${PROJECT_NAME} ${LIBTYPE} ${EC2_SRC})
add_library(AWS::${PROJECT_NAME} ALIAS ${PROJECT_NAME})

set_compiler_flags(${PROJECT_NAME})
set_compiler_warnings(${PROJECT_NAME})

if(USE_WINDOWS_DLL_SEMANTICS AND BUILD_SHARED_LIBS)
    target_compile_definitions(${PROJECT_NAME} PRIVATE "AWS_EC2_EXPORTS")
endif()

target_include_directories(${PROJECT_NAME} PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:include>)

target_link_libraries(${PROJECT_NAME} PRIVATE ${PLATFORM_DEP_LIBS} ${PROJECT_LIBS})
list(APPEND AWS_INSTALLED_LIBS "${PROJECT_NAME}")
set(AWS_INSTALLED_LIBS "${AWS_INSTALLED_LIBS}" PARENT_SCOPE)

setup_install()

install (FILES ${AWS_EC2_HEADERS} DESTINATION ${INCLUDE_DIRECTORY}/aws/ec2)
install (FILES ${AWS_EC2_MODEL_HEADERS} DESTINATION ${INCLUDE_DIRECTORY}/aws/ec2/model)

if(PLATFORM_WINDOWS AND MSVC)
    file(GLOB NUGET_FILES "nuget/${PROJECT_NAME}.*.autopkg")
    install (FILES ${NUGET_FILES} DESTINATION nuget)
endif()

do_packaging()


