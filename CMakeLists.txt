cmake_minimum_required(VERSION 2.8)
project(intel_pcm)
# Library target.
set(SRCS
    client_bw.cpp
    cpucounters.cpp
    cpucounterstest.cpp
    freegetopt/getopt.c
    memoptest.cpp
    msr.cpp
    msrtest.cpp
    pci.cpp
    # pcm3d/main.cpp
    # pcm3d/widget.cpp
    readmem.cpp
    realtime.cpp)
if (WIN32)
  list(APPEND SRCS
       Intelpcm.dll/dllmain.cpp
       Intelpcm.dll/Intelpcm.cpp
       PCM-Memory_Win/pcm-memory-win.cpp
       PCM-Memory_Win/stdafx.cpp
       PCM-MSR_Win/pcm-msr-win.cpp
       PCM-MSR_Win/stdafx.cpp
       PCM-NUMA_Win/pcm-numa-win.cpp
       PCM-NUMA_Win/stdafx.cpp
       PCM-PCIE_Win/pcm-pcie-win.cpp
       PCM-PCIE_Win/stdafx.cpp
       PCM-Power_Win/pcm-power-win.cpp
       PCM-Power_Win/stdafx.cpp
       PCM-Service_Win/AssemblyInfo.cpp
       PCM-Service_Win/PCMInstaller.cpp
       PCM-Service_Win/PCMService.cpp
       PCM-TSX_Win/pcm-tsx-win.cpp
       PCM-TSX_Win/stdafx.cpp
       PCM_Win/pcm.cpp
       PCM_Win/stdafx.cpp)
endif ()
if (UNIX)
  list(APPEND SRCS
       pcm-memory.cpp
       pcm-msr.cpp
       pcm-numa.cpp
       pcm-pcie.cpp
       pcm-power.cpp
       pcm-sensor.cpp
       pcm-tsx.cpp)
endif ()
if (WIN32)
  list(APPEND SRCS
       Intelpcm.dll/dllmain.cpp
       Intelpcm.dll/Intelpcm.cpp
       PCM-Memory_Win/pcm-memory-win.cpp
       PCM-Memory_Win/stdafx.cpp
       PCM-MSR_Win/pcm-msr-win.cpp
       PCM-MSR_Win/stdafx.cpp
       PCM-NUMA_Win/pcm-numa-win.cpp
       PCM-NUMA_Win/stdafx.cpp
       PCM-PCIE_Win/pcm-pcie-win.cpp
       PCM-PCIE_Win/stdafx.cpp
       PCM-Power_Win/pcm-power-win.cpp
       PCM-Power_Win/stdafx.cpp
       PCM-Service_Win/AssemblyInfo.cpp
       PCM-Service_Win/PCMInstaller.cpp
       PCM-Service_Win/PCMService.cpp
       PCM-TSX_Win/pcm-tsx-win.cpp
       PCM-TSX_Win/stdafx.cpp
       PCM_Win/pcm.cpp
       PCM_Win/stdafx.cpp
       WinMSRDriver/Win7/msrmain.c
       WinMSRDriver/WinXP/msrmain.c)
endif ()
add_library(intel_pcm ${SRCS})
set(LIBRARIES)
if (APPLE)
  set(PCM_MSR_DRIVER_SRCS
      MacMSRDriver/PcmMsr/PcmMsr.cpp
      MacMSRDriver/PcmMsr/PcmMsrClient.cpp)
  add_library(pcm_msr_driver ${PCM_MSR_DRIVER_SRCS})
  target_link_libraries(intel_pcm pcm_msr_driver)
  set(PCM_MSR_LIBRARY_SRCS
      MacMSRDriver/DriverInterface.c
      MacMSRDriver/MSRAccessor.cpp
      MacMSRDriver/PCIDriverInterface.cpp)
  add_library(pcm_msr_library ${PCM_MSR_LIBRARY_SRCS})
  target_link_libraries(intel_pcm pcm_msr_library)
  find_path(IOKIT_IOLIB_INCLUDE_DIR IOKit/IOLib.h)
  set_target_properties(
      pcm_msr_driver PROPERTIES COMPILE_FLAGS "-I${IOKIT_IOLIB_INCLUDE_DIR}/..")
  list(APPEND LIBRARIES $<TARGET_FILE:pcm_msr_driver> $<TARGET_FILE:pcm_msr_library>)
endif ()

if (NOT BUILD_SHARED_LIBS AND LIBRARIES)
  include(cmake/MergeStaticLibraries.cmake)
  merge_static_libraries(intel_pcm ALL "${LIBRARIES}")
endif ()

install(TARGETS intel_pcm
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib)
install(DIRECTORY ${PROJECT_SOURCE_DIR}/ DESTINATION include/intel_pcm
        FILES_MATCHING PATTERN *.h)
