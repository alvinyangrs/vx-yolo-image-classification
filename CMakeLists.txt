# CMakeLists.txt - yolo-imgcls-v1 build configuration
# Copyright (c) 2016,2017, 2020 Wind River Systems, Inc. All Rights Reserved.
#
# This simple cmake project exercises all relevant build options
# (C, C++ and Assembler; Library, Sharedlib and Executable; Testcases).
# The associated build.sh script shows sample command-lines for
# building in various environments.

# The syntax of this basic example works fine with cmake-2.8.7
# - cmake-2.8.9 or later is required for VxWorks Sharedlibs with Diab
# - cmake-3.3.0 or later is required for CROSSCOMPILING_EMULATOR to work
# - cmake-3.6.2 is recommended and has received most testing.
cmake_minimum_required(VERSION 2.8.7)

project(yolo-imgcls-v1 C CXX ASM)

set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -static")

SET(MY_TARGET ${PROJECT_NAME}.vxe)

set(VXWORKS_VSB_DIR "/media/sdc1/workspace/vxWorks/vx72403/workspace/nehalemvsb/")
set(TENSORFLOW_VXWORKS_INCLUDE "${VXWORKS_VSB_DIR}/usr/3pp/develop/usr/include/")



set(TENSORFLOW_SOURCE_DIR "${VXWORKS_VSB_DIR}/3pp/TENSORFLOW_LITE/tensorflow-2.12.0/")
set(TF_SOURCE_DIR "${TENSORFLOW_SOURCE_DIR}/tensorflow")
set(TFLITE_SOURCE_DIR "${TF_SOURCE_DIR}/lite")
set(TFNSORFLOW_PTHREADPOOL "${TFLITE_SOURCE_DIR}/build_user/pthreadpool")
set(TENSORFLOW_3APP "${TENSORFLOW_SOURCE_DIR}/tensorflow/lite/build_user/_deps/")
set(TENSORFLOW_3APP_RUY "${TENSORFLOW_3APP}/ruy-build/ruy/")
set(TENSORFLOW_3APP_ABSL "${TENSORFLOW_3APP}/abseil-cpp-build/absl")
set(TENSORFLOW_3APP_FARMHASH "${TENSORFLOW_3APP}/farmhash-build")
set(TENSORFLOW_3APP_FFT2D "${TENSORFLOW_3APP}/fft2d-build")
set(TENSORFLOW_3APP_FLATBUFFER "${TENSORFLOW_3APP}/flatbuffers-build")

link_directories(${TENSORFLOW_3APP_RUY}
              ${TENSORFLOW_3APP_RUY}/profiler)
link_directories(${TENSORFLOW_3APP_ABSL}/base 
              ${TENSORFLOW_3APP_ABSL}/container 
              ${TENSORFLOW_3APP_ABSL}/debugging 
              ${TENSORFLOW_3APP_ABSL}/flags 
              ${TENSORFLOW_3APP_ABSL}/hash 
              ${TENSORFLOW_3APP_ABSL}/status
              ${TENSORFLOW_3APP_ABSL}/numeric
              ${TENSORFLOW_3APP_ABSL}/profiling
              ${TENSORFLOW_3APP_ABSL}/random
              ${TENSORFLOW_3APP_ABSL}/strings
              ${TENSORFLOW_3APP_ABSL}/synchronization
              ${TENSORFLOW_3APP_ABSL}/time
              ${TENSORFLOW_3APP_ABSL}/types
              )
link_directories(${TENSORFLOW_3APP_FARMHASH})
link_directories(${TENSORFLOW_3APP_FFT2D})
link_directories(${TENSORFLOW_3APP_FLATBUFFER})
link_directories(${TFNSORFLOW_PTHREADPOOL})

#macro(populate_source_vars SOURCE_DIR SOURCES_VAR)
#  cmake_parse_arguments(ARGS "RECURSE" "" "FILTER" ${ARGN})
#  if(ARGS_RECURSE)
#    set(GLOB_OP GLOB_RECURSE)
#  else()
#    set(GLOB_OP GLOB)
#  endif()
#  set(DEFAULT_FILE_FILTER ".*(_test|test_util)\\.(c|cc|h)$")
#  file(${GLOB_OP} FOUND_SOURCES "${SOURCE_DIR}/*.*")
#  list(FILTER FOUND_SOURCES INCLUDE REGEX ".*\\.(c|cc|h)$")
#  list(FILTER FOUND_SOURCES EXCLUDE REGEX "${DEFAULT_FILE_FILTER}")
#  foreach(FILE_FILTER ${ARGS_FILTER})
#    list(FILTER FOUND_SOURCES EXCLUDE REGEX "${FILE_FILTER}")
#  endforeach()
#  list(APPEND ${SOURCES_VAR} ${FOUND_SOURCES})
#endmacro()

#macro(populate_tf_source_vars RELATIVE_DIR SOURCES_VAR)
#  populate_source_vars(
#    "${TF_SOURCE_DIR}/${RELATIVE_DIR}" ${SOURCES_VAR} ${ARGN}
#  )
#endmacro()

#populate_source_vars("${TFLITE_SOURCE_DIR}/examples/label_image"
#  TFLITE_LABEL_IMAGE_SRCS
#  FILTER "_test\\.cc$"
#)

set(TFLITE_LABEL_IMAGE_SRCS
    label_image.cc
    bitmap_helpers.cc
    )

list(APPEND TFLITE_LABEL_IMAGE_SRCS
  ${TF_SOURCE_DIR}/tsl/util/stats_calculator.cc
  ${TFLITE_SOURCE_DIR}/profiling/memory_info.cc
  ${TFLITE_SOURCE_DIR}/profiling/profile_summarizer.cc
  ${TFLITE_SOURCE_DIR}/profiling/profile_summary_formatter.cc
  ${TFLITE_SOURCE_DIR}/profiling/time.cc
  ${TFLITE_SOURCE_DIR}/tools/command_line_flags.cc
  ${TFLITE_SOURCE_DIR}/tools/delegates/default_execution_provider.cc
  ${TFLITE_SOURCE_DIR}/tools/delegates/delegate_provider.cc
  ${TFLITE_SOURCE_DIR}/tools/evaluation/utils.cc
  ${TFLITE_SOURCE_DIR}/tools/tool_params.cc
)

if(TFLITE_ENABLE_XNNPACK)
  list(APPEND TFLITE_LABEL_IMAGE_SRCS
    ${TFLITE_SOURCE_DIR}/tools/delegates/xnnpack_delegate_provider.cc
  )
else()
  set(TFLITE_LABEL_IMAGE_CC_OPTIONS "-DTFLITE_WITHOUT_XNNPACK")
endif()  # TFLITE_ENABLE_XNNPACK


add_executable(${MY_TARGET}
	${TFLITE_LABEL_IMAGE_SRCS}
)

target_compile_options(${MY_TARGET}
  PRIVATE
    ${TFLITE_LABEL_IMAGE_CC_OPTIONS}
    -XXXXXXXXXX
)

target_link_libraries(${MY_TARGET}
tensorflow-lite 
absl_flags 
absl_flags_internal 
absl_flags_marshalling 
absl_flags_reflection 
absl_flags_config 
absl_flags_program_name
absl_flags_private_handle_accessor
absl_flags_commandlineflag
absl_flags_commandlineflag_internal
absl_raw_hash_set
absl_hashtablez_sampler
absl_hash
absl_city
absl_low_level_hash
absl_status
absl_cord
absl_bad_optional_access
absl_cordz_info
absl_cord_internal
absl_cordz_functions
absl_exponential_biased
absl_cordz_handle
absl_str_format_internal
absl_strerror
absl_synchronization
absl_stacktrace
absl_symbolize
absl_debugging_internal
absl_demangle_internal
absl_graphcycles_internal
absl_malloc_internal
absl_time
absl_strings
absl_strings_internal
absl_throw_delegate.a 
absl_base
absl_spinlock_wait
absl_int128
absl_civil_time
absl_time_zone
absl_bad_variant_access
absl_raw_logging_internal
absl_log_severity
farmhash
fft2d_fftsg2d
fft2d_fftsg
flatbuffers
ruy_context_get_ctx
ruy_context
ruy_frontend
ruy_kernel_arm
ruy_kernel_avx
ruy_kernel_avx2_fma
ruy_kernel_avx512
ruy_apply_multiplier
ruy_pack_arm
ruy_pack_avx
ruy_pack_avx2_fma
ruy_pack_avx512
ruy_prepare_packed_matrices
ruy_trmul
ruy_ctx
ruy_allocator
ruy_prepacked_cache
ruy_system_aligned_alloc
ruy_have_built_path_for_avx
ruy_have_built_path_for_avx2_fma
ruy_have_built_path_for_avx512
ruy_thread_pool
ruy_blocking_counter
ruy_wait
ruy_denormal
ruy_block_map
ruy_tune
ruy_cpuinfo
ruy_profiler_instrumentation
pthreadpool
)


include_directories(
    ${TENSORFLOW_VXWORKS_INCLUDE}
    ${TENSORFLOW_SOURCE_DIR}
  )

IF(EXISTS "${WIND_CC_SYSROOT}/secureLoader/privatekey.pem")
	IF(EXISTS "${WIND_CC_SYSROOT}/secureLoader/publiccert.pem")
		add_custom_command(TARGET ${MY_TARGET} POST_BUILD
			COMMAND echo "start singtool command ..."
			COMMAND signtool --strip=all --key=${WIND_CC_SYSROOT}/secureLoader/privatekey.pem --cert=${WIND_CC_SYSROOT}/secureLoader/publiccert.pem --type=ELF ${MY_TARGET}
			COMMENT  "end singtool command ..."
		)
	ENDIF()
ENDIF()

