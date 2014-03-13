cmake_minimum_required(VERSION 2.8)
project(intel_pcm)
# Library target.
set(SRCS cpucounters.cpp)
add_library(intel_pcm ${SRCS})
install(TARGETS intel_pcm
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib)
install(DIRECTORY ${PROJECT_SOURCE_DIR} DESTINATION include/intel_pcm
        FILES_MATCHING PATTERN *.h)
