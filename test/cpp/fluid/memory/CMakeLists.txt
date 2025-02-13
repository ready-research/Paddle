cc_test(
  memory_stats_test
  SRCS memory_stats_test.cc
  DEPS)
cc_test(
  stats_test
  SRCS stats_test.cc
  DEPS)

cc_test(
  naive_best_fit_allocator_test
  SRCS naive_best_fit_allocator_test.cc
  DEPS allocator)
cc_test(
  buffered_allocator_test
  SRCS buffered_allocator_test.cc
  DEPS allocator)

if(WITH_GPU)
  nv_test(
    thread_local_allocator_test
    SRCS thread_local_allocator_test.cc
    DEPS allocator)
endif()
if(WITH_ROCM)
  hip_test(
    thread_local_allocator_test
    SRCS thread_local_allocator_test.cc
    DEPS allocator)
endif()

if(WITH_GPU)
  nv_test(
    best_fit_allocator_test
    SRCS best_fit_allocator_test.cc best_fit_allocator_test.cu
    DEPS allocator)
elseif(WITH_ROCM)
  hip_test(
    best_fit_allocator_test
    SRCS best_fit_allocator_test.cc best_fit_allocator_test.cu
    DEPS allocator)
else()
  cc_test(
    best_fit_allocator_test
    SRCS best_fit_allocator_test.cc
    DEPS allocator)
endif()

cc_test(
  test_aligned_allocator
  SRCS test_aligned_allocator.cc
  DEPS allocator)

cc_test(
  retry_allocator_test
  SRCS retry_allocator_test.cc
  DEPS allocator)
if(TEST retry_allocator_test)
  set_tests_properties(retry_allocator_test PROPERTIES LABELS
                                                       "RUN_TYPE=EXCLUSIVE")
endif()

cc_test(
  allocator_facade_abs_flags_test
  SRCS allocator_facade_abs_flags_test.cc
  DEPS allocator)

cc_test(
  allocator_facade_frac_flags_test
  SRCS allocator_facade_frac_flags_test.cc
  DEPS allocator)

if(WITH_GPU)
  nv_test(
    malloc_test
    SRCS malloc_test.cu
    DEPS device_context)
  nv_test(
    stream_safe_cuda_alloc_test
    SRCS stream_safe_cuda_alloc_test.cu
    DEPS cuda_graph_with_memory_pool)
  nv_test(
    cuda_managed_memory_test
    SRCS cuda_managed_memory_test.cu
    DEPS gpu_info place)

  if(WITH_TESTING AND TEST stream_safe_cuda_alloc_test)
    set_tests_properties(
      stream_safe_cuda_alloc_test
      PROPERTIES ENVIRONMENT "FLAGS_use_stream_safe_cuda_allocator=true; \
        FLAGS_allocator_strategy=auto_growth")
  endif()
endif()

if(WITH_ROCM)
  hip_test(
    malloc_test
    SRCS malloc_test.cu
    DEPS device_context)
  hip_test(
    cuda_managed_memory_test
    SRCS cuda_managed_memory_test.cu
    DEPS gpu_info place)
endif()

if(WITH_TESTING AND TEST cuda_managed_memory_test)
  set_tests_properties(
    cuda_managed_memory_test
    PROPERTIES
      ENVIRONMENT
      "FLAGS_use_cuda_managed_memory=true;FLAGS_allocator_strategy=auto_growth"
      TIMEOUT 50)
endif()

if(WITH_GPU AND WITH_TESTING)
  nv_test(
    get_base_ptr_test
    SRCS get_base_ptr_test.cu
    DEPS gpu_info)
  set_tests_properties(
    get_base_ptr_test
    PROPERTIES ENVIRONMENT "FLAGS_allocator_strategy=auto_growth;
                                    FLAGS_use_stream_safe_cuda_allocator=true;")
endif()

cc_test(
  auto_growth_best_fit_allocator_facade_test
  SRCS auto_growth_best_fit_allocator_facade_test.cc
  DEPS allocator)
cc_test(
  auto_growth_best_fit_allocator_test
  SRCS auto_growth_best_fit_allocator_test.cc
  DEPS allocator)

if(NOT WIN32)
  cc_test(
    mmap_allocator_test
    SRCS mmap_allocator_test.cc
    DEPS allocator)
endif()

cc_test(
  system_allocator_test
  SRCS system_allocator_test.cc
  DEPS allocator)

cc_test(
  buddy_allocator_test
  SRCS buddy_allocator_test.cc
  DEPS allocator)
