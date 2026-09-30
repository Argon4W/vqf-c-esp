# VQF-C

C port of [dlaidig/vqf](https://github.com/dlaidig/vqf) in object-oriented C style as an ESP-IDF component. This VQF is derived from the original C++ implementation of the VQF.

## Advantage

This library separates the implementation details of linear algebra from the VQF fusion algorithm, making it possible to attach any linear algebra library (e.g. [Eigen](https://libeigen.gitlab.io/)) to this algorithm to integrate with your project.

## Features

This C port supports the following features of the original C++ implementation:
- 6D sensor fusion.
- 9D sensor fusion.
- Rest detection.
- Magnetic disturbance detection.
- Rest bias estimation.
- Motion bias estimation.

---

## Usage

The following example shows how to use this library to perform simultaneous 6D (magnetometer-free) and 9D (gyr+acc+mag) sensor fusion.

```c++
#include <stddef.h>
#include <math.h>

#include "vqf.h"

void calibrate() {
    // Setup the math context.
    static const vqf_linear_algebra_t vqf_linear_algebra = {
        .new_matrix = ...,
        .delete_matrix = ...,
        .is_matrix_zeros = ...,
        .get_matrix_coefficient = ...,
        .set_matrix_coefficient = ...,
        .add_matrix_coefficient = ...,
        .copy_matrix = ...,
        .add_matrix = ...,
        .subtract_matrix = ...,
        .multiply_matrix = ...,
        .clip_matrix = ...,
        .abs_matrix = ...,
        .invert_matrix = ...,
        .transpose_matrix = ...,
        .normalize_matrix = ...,
        .multiply_matrix_scalar = ...,
        .set_matrix_zeros = ...,
        .set_matrix_scaled_identity = ...,
        .get_vector_norm = ...,
        .get_vector_squared_norm = ...,
        .sum_matrix_column_vectors = ...,
        .new_matrix_double = ...,
        .delete_matrix_double = ...,
        .get_matrix_double_coefficient = ...,
        .set_matrix_double_coefficient = ...,
        .add_matrix_double_coefficient = ...,
        .copy_matrix_double = ...,
        .add_matrix_double = ...,
        .accumulate_matrix_double = ...,
        .multiply_matrix_double_scalar = ...,
        .set_matrix_double_zeros = ...,
        .set_matrix_double_constants = ...,
        .copy_matrix_to_matrix_double = ...,
        .copy_matrix_double_to_matrix = ...,
        .new_quaternion = ...,
        .delete_quaternion = ...,
        .set_quaternion_w = ...,
        .set_quaternion_x = ...,
        .set_quaternion_y = ...,
        .set_quaternion_z = ...,
        .copy_quaternion = ...,
        .multiply_quaternion = ...,
        .rotate_quaternion_around_z = ...,
        .set_quaternion_rotation = ...,
        .quaternion_rotate_vector = ...,
        .quaternion_to_rotation_matrix = ...,
        .normalize_quaternion = ...,
        .set_quaternion_identity = ...
    };

    // Create a VQF context.
    vqf_context_t* vqf_context = vqf_context_new(
        vqf_linear_algebra,
        vqf_params_default, // The default parameters.
        ..., // Sample time of gyroscope in milliseconds.
        ..., // Sample time of accelerometer in milliseconds.
        ... // Sample time of magnetometer in milliseconds.
    )
    
    // Do the gyroscope update.
    vqf_update_gyr(vqf_context, ...);

    // Do the accelerometer update.
    vqf_update_acc(vqf_context, ...);

    // Do the magnetometer update.
    vqf_update_mag(vqf_context, ...);

    // Get the fused 6D orientation.
    vqf_get_quat_6D(vqf_context, ...);

    // Get the fused 9D orientation.
    vqf_get_quat_9D(vqf_context, ...);
}
```