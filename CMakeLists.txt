cmake_minimum_required(VERSION 3.28)
project(fixNES)

set(CMAKE_C_STANDARD 99)
set(CMAKE_CXX_STANDARD 20)

include(FetchContent)

FetchContent_Declare(freeglut
    GIT_REPOSITORY https://github.com/freeglut/freeglut
    GIT_TAG v3.6.0
    GIT_PROGRESS TRUE
    GIT_SHALLOW TRUE
)
FetchContent_MakeAvailable(freeglut)

FetchContent_Declare(openal-soft
    GIT_REPOSITORY https://github.com/kcat/openal-soft
    GIT_TAG openal-soft-1.21.0
    GIT_PROGRESS TRUE
    GIT_SHALLOW TRUE
)
set(LIBTYPE "STATIC")
FetchContent_MakeAvailable(openal-soft)

FetchContent_Declare(nfd
        GIT_REPOSITORY https://github.com/btzy/nativefiledialog-extended
        GIT_TAG v1.2.1
        GIT_PROGRESS TRUE
        GIT_SHALLOW TRUE
)
set(LIBTYPE "STATIC")
FetchContent_MakeAvailable(nfd)

find_package(OpenGL REQUIRED)

file(GLOB MAPPER_SRCS src/mapper/*.c)

add_executable(fixNES
        src/main.c
        src/apu.c
        src/audio.c
        src/audio_fds.c
        src/audio_mmc5.c
        src/audio_vrc6.c
        src/audio_vrc7.c
        src/audio_n163.c
        src/audio_s5b.c
        src/alhelpers.c
        src/cpu.c
        src/ppu.c
        src/mem.c
        src/input.c
        src/mapper.c
        src/mapperList.c
        src/fm2play.c
        src/vrc_irq.c
        ${MAPPER_SRCS}
)
target_compile_definitions(fixNES PRIVATE FREEGLUT_STATIC DO_INLINE_ATTRIBS=0)
if (WIN32)
    target_compile_definitions(fixNES PRIVATE WINDOWS_BUILD)
    target_include_directories(fixNES PRIVATE ${freeglut_SOURCE_DIR}/include)
elseif (UNIX)
    target_compile_definitions(fixNES PRIVATE ZIPSUPPORT)
    target_sources(fixNES src/unzip/ioapi_mem.c src/unzip/unzip.c)
endif (WIN32)
target_link_libraries(fixNES PRIVATE freeglut_static OpenAL OpenGL::GL OpenGL::GLU nfd::nfd)

