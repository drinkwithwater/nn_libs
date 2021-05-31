
cmake_minimum_required(VERSION 2.8)

project(tflite)

add_definitions( -DNDEBUG )
add_definitions( -D__LITTLE_ENDIAN__ )
add_definitions( -DTFLITE_WITHOUT_XNNPACK )

if (WIN32)
	add_definitions( -DTFL_COMPILE_LIBRARY )
	add_definitions( -DNOMINMAX )
	add_definitions( -DFARMHASH_NO_BUILTIN_EXPECT )
	add_compile_options( /O2 )
else ()
	add_compile_options( -fPIC )
	add_compile_options( -O3 )
endif ()

set(TENSORFLOW_PATH ${CMAKE_CURRENT_SOURCE_DIR}/../../../../github/tensorflow)

set(MAKEFILE_DIR ${TENSORFLOW_PATH}/tensorflow/lite/tools/make)
include_directories(
	${TENSORFLOW_PATH}
	${MAKEFILE_DIR}/../../../../..
	${MAKEFILE_DIR}/../../../../../..
	${MAKEFILE_DIR}/downloads/
	${MAKEFILE_DIR}/downloads/eigen
	${MAKEFILE_DIR}/downloads/absl
	${MAKEFILE_DIR}/downloads/gemmlowp
	${MAKEFILE_DIR}/downloads/ruy
	${MAKEFILE_DIR}/downloads/neon_2_sse
	${MAKEFILE_DIR}/downloads/farmhash/src
	${MAKEFILE_DIR}/downloads/flatbuffers/include
	${MAKEFILE_DIR}/downloads/fp16/include
)

# set CORE_CC_ALL_SRCS
file(GLOB CORE_CC_ALL_SRCS
	${TENSORFLOW_PATH}/tensorflow/lite/*.cc
	${TENSORFLOW_PATH}/tensorflow/lite/*.c
	${TENSORFLOW_PATH}/tensorflow/lite/c/*.c
	${TENSORFLOW_PATH}/tensorflow/lite/c/*.cc
	${TENSORFLOW_PATH}/tensorflow/lite/core/*.cc
	${TENSORFLOW_PATH}/tensorflow/lite/core/api/*.cc
	${TENSORFLOW_PATH}/tensorflow/lite/experimental/resource/*.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/ruy/ruy/*.cc

	# nor micro
	${TENSORFLOW_PATH}/tensorflow/lite/kernels/*.cc
	${TENSORFLOW_PATH}/tensorflow/lite/kernels/internal/*.cc
	${TENSORFLOW_PATH}/tensorflow/lite/kernels/internal/optimized/*.cc
	${TENSORFLOW_PATH}/tensorflow/lite/kernels/internal/reference/*.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/optimize/sparsity/*.cc

	# PROFILER_SRCS
	${TENSORFLOW_PATH}/tensorflow/lite/profiling/memory_info.cc
	${TENSORFLOW_PATH}/tensorflow/lite/profiling/platform_profiler.cc
	${TENSORFLOW_PATH}/tensorflow/lite/profiling/time.cc

	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/farmhash/src/farmhash.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/fft2d/fftsg.c
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/fft2d/fftsg2d.c
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/flatbuffers/src/util.cpp

	#find downloads/absl/absl/ -type f -name \*.cc | grep -v test | grep -v benchmark | grep -v synchronization | grep -v debugging | grep -v hash | grep -v flags | grep -v random
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/base/dynamic_annotations.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/base/internal/cycleclock.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/base/internal/exponential_biased.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/base/internal/low_level_alloc.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/base/internal/periodic_sampler.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/base/internal/raw_logging.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/base/internal/scoped_set_env.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/base/internal/spinlock.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/base/internal/spinlock_wait.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/base/internal/sysinfo.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/base/internal/thread_identity.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/base/internal/throw_delegate.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/base/internal/unscaledcycleclock.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/base/log_severity.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/numeric/int128.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/status/status.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/status/status_payload_printer.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/strings/ascii.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/strings/charconv.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/strings/cord.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/strings/escaping.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/strings/internal/charconv_bigint.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/strings/internal/charconv_parse.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/strings/internal/escaping.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/strings/internal/memutil.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/strings/internal/ostringstream.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/strings/internal/pow10_helper.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/strings/internal/str_format/arg.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/strings/internal/str_format/bind.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/strings/internal/str_format/extension.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/strings/internal/str_format/float_conversion.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/strings/internal/str_format/output.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/strings/internal/str_format/parser.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/strings/internal/utf8.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/strings/match.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/strings/numbers.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/strings/string_view.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/strings/str_cat.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/strings/str_replace.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/strings/str_split.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/strings/substitute.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/time/civil_time.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/time/clock.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/time/duration.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/time/format.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/time/internal/cctz/src/civil_time_detail.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/time/internal/cctz/src/time_zone_fixed.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/time/internal/cctz/src/time_zone_format.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/time/internal/cctz/src/time_zone_if.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/time/internal/cctz/src/time_zone_impl.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/time/internal/cctz/src/time_zone_info.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/time/internal/cctz/src/time_zone_libc.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/time/internal/cctz/src/time_zone_lookup.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/time/internal/cctz/src/time_zone_posix.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/time/internal/cctz/src/zone_info_source.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/time/time.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/types/bad_any_cast.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/types/bad_optional_access.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/absl/absl/types/bad_variant_access.cc
)

list(APPEND CORE_CC_ALL_SRCS
	${TENSORFLOW_PATH}/tensorflow/lite/delegates/nnapi/nnapi_delegate_disabled.cc
	${TENSORFLOW_PATH}/tensorflow/lite/nnapi/nnapi_implementation_disabled.cc
)

if (WIN32)
	include_directories(
		${CMAKE_CURRENT_SOURCE_DIR}/mman_win32
	)
	list(APPEND CORE_CC_ALL_SRCS
		${CMAKE_CURRENT_SOURCE_DIR}/mman_win32/sys/mman.c
	)
endif ()

file(GLOB CORE_CC_EXCLUDE_SRCS
	${TENSORFLOW_PATH}/tensorflow/lite/*test.cc
	${TENSORFLOW_PATH}/tensorflow/lite/*/*test.cc
	${TENSORFLOW_PATH}/tensorflow/lite/*/*/benchmark.cc
	${TENSORFLOW_PATH}/tensorflow/lite/*/*/example*.cc
	${TENSORFLOW_PATH}/tensorflow/lite/*/*/test*.cc
	${TENSORFLOW_PATH}/tensorflow/lite/*/*/*test.cc
	${TENSORFLOW_PATH}/tensorflow/lite/*/*/*tool.cc
	${TENSORFLOW_PATH}/tensorflow/lite/*/*/*/benchmark.cc
	${TENSORFLOW_PATH}/tensorflow/lite/*/*/*/example*.cc
	${TENSORFLOW_PATH}/tensorflow/lite/*/*/*/test*.cc
	${TENSORFLOW_PATH}/tensorflow/lite/*/*/*/*test.cc
	${TENSORFLOW_PATH}/tensorflow/lite/*/*/*/*tool.cc
	${TENSORFLOW_PATH}/tensorflow/lite/*/*/*/*/*/benchmark.cc
	${TENSORFLOW_PATH}/tensorflow/lite/*/*/*/*/*/example*.cc
	${TENSORFLOW_PATH}/tensorflow/lite/*/*/*/*/*/test*.cc
	${TENSORFLOW_PATH}/tensorflow/lite/*/*/*/*/*/*test.cc
	${TENSORFLOW_PATH}/tensorflow/lite/*/*/*/*/*/*tool.cc
	${TENSORFLOW_PATH}/tensorflow/lite/kernels/*test_main.cc
	${TENSORFLOW_PATH}/tensorflow/lite/kernels/*test_util*.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tflite_with_xnnpack.cc
	${TENSORFLOW_PATH}/tensorflow/lite/examples/minimal/minimal.cc
)

list(APPEND CORE_CC_EXCLUDE_SRCS
	${TENSORFLOW_PATH}/tensorflow/lite/mmap_allocation_disabled.cc
	${TENSORFLOW_PATH}/tensorflow/lite/minimal_logging_android.cc
	${TENSORFLOW_PATH}/tensorflow/lite/minimal_logging_ios.cc
	${TENSORFLOW_PATH}/tensorflow/lite/tools/make/downloads/ruy/ruy/prepare_packed_matrices.cc
)

list(REMOVE_ITEM CORE_CC_ALL_SRCS ${CORE_CC_EXCLUDE_SRCS})

#message(${CORE_CC_ALL_SRCS})

add_library( tensorflow-lite STATIC ${CORE_CC_ALL_SRCS})
add_executable( minimal ${CORE_CC_ALL_SRCS} ${TENSORFLOW_PATH}/tensorflow/lite/examples/minimal/minimal.cc)
if (WIN32)
else ()
	target_link_libraries( tensorflow-lite stdc++ pthread m z dl)
	target_link_libraries( minimal stdc++ pthread m z dl)
endif ()
