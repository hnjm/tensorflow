load("@flatbuffers//:build_defs.bzl", "flatbuffer_cc_library")
load("//tensorflow:strict.default.bzl", "py_strict_binary", "py_strict_library", "py_strict_test")
load("//tensorflow:tensorflow.default.bzl", "get_compatible_with_portable")
load("//tensorflow/lite:special_rules.bzl", "tflite_portable_test_suite", "tflite_schema_utils_friends")

package(
    # copybara:uncomment default_applicable_licenses = ["//tensorflow:license"],
    default_visibility = [
        "//visibility:public",
    ],
    licenses = ["notice"],
)

filegroup(
    name = "tflite_internal_cc_3p_api_deps_src",
    srcs = [
        ":schema_fbs_srcs",
        ":schema_utils.h",
    ],
    visibility = [
        "//tensorflow/lite:__pkg__",
    ],
)

# This is the package group declaration to which targets for TensorFlow Lite
# Flatbuffer schema utilities.
#
# Its usage should be rare, and is often abused by tools that are doing
# Flatbuffer creation/manipulation in unofficially supported ways.
package_group(
    name = "utils_friends",
    packages = [
        "//tensorflow/compiler/mlir/lite/...",
        "//tensorflow/lite/...",
    ] + tflite_schema_utils_friends(),
)

py_strict_binary(
    name = "upgrade_schema",
    srcs = ["upgrade_schema.py"],
    deps = [":upgrade_schema_main_lib"],
)

py_strict_library(
    name = "upgrade_schema_main_lib",
    srcs = [
        "upgrade_schema.py",
    ],
    data = [
        "//tensorflow/compiler/mlir/lite/schema:schema_v0.fbs",
        "//tensorflow/compiler/mlir/lite/schema:schema_v1.fbs",
        "//tensorflow/compiler/mlir/lite/schema:schema_v2.fbs",
        "//tensorflow/compiler/mlir/lite/schema:schema_v3.fbs",
        "@flatbuffers//:flatc",
    ],
    deps = [
        "//tensorflow:tensorflow_py",
        "//tensorflow/python/platform:resource_loader",
    ],
)

py_strict_test(
    name = "upgrade_schema_test",
    size = "small",
    srcs = ["upgrade_schema_test.py"],
    tags = [
        "manual",
        "no_oss",
        "no_pip",
        "notap",
    ],
    deps = [
        ":upgrade_schema_main_lib",
        "//tensorflow/python/framework:test_lib",
        "//tensorflow/python/platform:client_testlib",
    ],
)

# copybara:uncomment_begin(google-only)
# py_strict_test(
#     name = "schema_validation_test",
#     srcs = ["schema_validation_test.py"],
#     data = [
#         ":schema_fbs_srcs",
#         ":schema_generated.h.oss",
#     ],
#     # TODO(b/235550563): Enable this TAP with FlatBuffer 2.0.6 migration.
#     tags = [
#         "manual",
#         "notap",
#     ],
#     deps = [
#         "//testing/pybase",
#         "@absl_py//absl/flags",
#     ],
# )
# copybara:uncomment_end

exports_files([
    "conversion_metadata.fbs",
])

# copybara:uncomment_begin(google-only)
# flatbuffer_cc_library(
#     name = "schema_fbs",
#     srcs = ["//tensorflow/compiler/mlir/lite/schema:schema.fbs"],
#     compatible_with = get_compatible_with_portable(),
# )
# copybara:uncomment_end(google-only)

cc_library(
    name = "schema_fbs",  # copybara:comment_replace name = "schema_fbs_for_oss",
    hdrs = [
        ":schema_generated.h",
        "//tensorflow/compiler/mlir/lite/schema:schema_generated.h",
    ],
    deps = [
        "//tensorflow/compiler/mlir/lite/schema:schema_fbs",
        "@flatbuffers//:runtime_cc",
    ],
)

# Generic schema for flatbuffer converter (but with mutable makes bigger).
flatbuffer_cc_library(
    name = "schema_fbs_with_mutable",
    srcs = ["//tensorflow/compiler/mlir/lite/schema:schema.fbs"],
    compatible_with = get_compatible_with_portable(),
    flatc_args = [
        "--gen-mutable",
        "--gen-object-api",
    ],
    out_prefix = "mutable/",
)

# Generic schema for inference on device (but with reflections makes bigger).
flatbuffer_cc_library(
    name = "schema_fbs_with_reflection",
    srcs = ["//tensorflow/compiler/mlir/lite/schema:schema.fbs"],
    compatible_with = get_compatible_with_portable(),
    flatc_args = [
        "--reflect-types",
        "--reflect-names",
        "--no-union-value-namespacing",
        "--gen-object-api",
    ],
    out_prefix = "reflection/",
)

cc_library(
    name = "schema_utils",
    hdrs = ["schema_utils.h"],
    compatible_with = get_compatible_with_portable(),
    visibility = ["//visibility:public"],
    deps = ["//tensorflow/compiler/mlir/lite/schema:schema_utils"],
)

cc_library(
    name = "schema_conversion_utils",
    hdrs = ["schema_conversion_utils.h"],
    compatible_with = get_compatible_with_portable(),
    visibility = ["//visibility:public"],
    deps = ["//tensorflow/compiler/mlir/lite/schema:schema_conversion_utils"],
)

flatbuffer_cc_library(
    name = "conversion_metadata_fbs",
    srcs = ["//tensorflow/compiler/mlir/lite/schema:conversion_metadata.fbs"],
    compatible_with = get_compatible_with_portable(),
)

tflite_portable_test_suite()
