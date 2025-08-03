.. _chapter-developer-configuration-guide:

=======================
Configuration Guide
=======================

Overview
========

This guide provides comprehensive information about all configuration parameters available in Stella VSLAM. The system uses YAML configuration files to control all aspects of the SLAM system, from camera setup to algorithm parameters.

Configuration Structure
======================

All configurations are defined in YAML files with the following structure:

.. code-block:: yaml

    # Camera configuration
    Camera:
      # Camera parameters...

    # System configuration
    System:
      # System parameters...

    # Feature extraction
    Feature:
      # Feature parameters...

    # Preprocessing
    Preprocessing:
      # Preprocessing parameters...

    # Tracking
    Tracking:
      # Tracking parameters...

    # Mapping
    Mapping:
      # Mapping parameters...

    # Loop detection
    LoopDetector:
      # Loop detection parameters...

    # Global optimization
    GlobalOptimizer:
      # Global optimization parameters...

Configuration Hierarchy
======================

The configuration system follows a hierarchical structure:

.. mermaid::

    graph TB
        subgraph "Configuration System"
            YAML[YAML File]
            Parser[Configuration Parser]
            ConfigObj[Configuration Object]
            
            subgraph "Configuration Categories"
                Camera[Camera Config]
                System[System Config]
                Feature[Feature Config]
                Tracking[Tracking Config]
                Mapping[Mapping Config]
                LoopDet[Loop Detection Config]
                GlobalOpt[Global Optimisation Config]
            end
            
            subgraph "Runtime Components"
                CameraModels[Camera Models]
                FeatureExt[Feature Extraction]
                TrackingMod[Tracking Module]
                MappingMod[Mapping Module]
                LoopDetMod[Loop Detector]
                GlobalOptMod[Global Optimiser]
            end
        end
        
        YAML --> Parser
        Parser --> ConfigObj
        ConfigObj --> Camera
        ConfigObj --> System
        ConfigObj --> Feature
        ConfigObj --> Tracking
        ConfigObj --> Mapping
        ConfigObj --> LoopDet
        ConfigObj --> GlobalOpt
        
        Camera --> CameraModels
        Feature --> FeatureExt
        Tracking --> TrackingMod
        Mapping --> MappingMod
        LoopDet --> LoopDetMod
        GlobalOpt --> GlobalOptMod

Camera Configuration
===================

Basic Camera Information
-----------------------

+-------------+----------+--------------------------------+----------+----------------------------------+
| Parameter   | Type     | Description                    | Default  | Example                          |
+=============+==========+================================+==========+==================================+
| ``name``    | string   | Camera identifier             | -        | ``"RICOH THETA S 960"``          |
+-------------+----------+--------------------------------+----------+----------------------------------+
| ``setup``   | string   | Camera setup type             | -        | ``"monocular"``, ``"stereo"``,   |
|             |          |                                |          | ``"RGBD"``                       |
+-------------+----------+--------------------------------+----------+----------------------------------+
| ``model``   | string   | Camera model type             | -        | ``"perspective"``, ``"fisheye"``,|
|             |          |                                |          | ``"equirectangular"``,          |
|             |          |                                |          | ``"radial_division"``            |
+-------------+----------+--------------------------------+----------+----------------------------------+

Intrinsic Parameters
-------------------

+--------+----------+--------------------------------+----------+----------+------------+
| Param  | Type     | Description                    | Units    | Default  | Example    |
+========+==========+================================+==========+==========+============+
| ``fx`` | double   | Focal length in x-direction   | pixels   | -        | ``718.856``|
+--------+----------+--------------------------------+----------+----------+------------+
| ``fy`` | double   | Focal length in y-direction   | pixels   | -        | ``718.856``|
+--------+----------+--------------------------------+----------+----------+------------+
| ``cx`` | double   | Principal point x-coordinate   | pixels   | -        | ``607.1928``|
+--------+----------+--------------------------------+----------+----------+------------+
| ``cy`` | double   | Principal point y-coordinate   | pixels   | -        | ``185.2157``|
+--------+----------+--------------------------------+----------+----------+------------+

Distortion Parameters
--------------------

Perspective Camera
~~~~~~~~~~~~~~~~~

+--------+----------+--------------------------------+----------+------------+
| Param  | Type     | Description                    | Default  | Example    |
+========+==========+================================+==========+============+
| ``k1`` | double   | Radial distortion coefficient 1| ``0.0``  | ``0.262383``|
+--------+----------+--------------------------------+----------+------------+
| ``k2`` | double   | Radial distortion coefficient 2| ``0.0``  | ``-0.953104``|
+--------+----------+--------------------------------+----------+------------+
| ``p1`` | double   | Tangential distortion coeff. 1 | ``0.0``  | ``-0.005358``|
+--------+----------+--------------------------------+----------+------------+
| ``p2`` | double   | Tangential distortion coeff. 2 | ``0.0``  | ``0.002628``|
+--------+----------+--------------------------------+----------+------------+
| ``k3`` | double   | Radial distortion coefficient 3| ``0.0``  | ``1.163314``|
+--------+----------+--------------------------------+----------+------------+

Fisheye Camera
~~~~~~~~~~~~~~

+--------+----------+--------------------------------+----------+----------+
| Param  | Type     | Description                    | Default  | Example  |
+========+==========+================================+==========+==========+
| ``k1`` | double   | Fisheye distortion coeff. 1   | ``0.0``  | ``0.1``  |
+--------+----------+--------------------------------+----------+----------+
| ``k2`` | double   | Fisheye distortion coeff. 2   | ``0.0``  | ``0.05`` |
+--------+----------+--------------------------------+----------+----------+
| ``k3`` | double   | Fisheye distortion coeff. 3   | ``0.0``  | ``0.0``  |
+--------+----------+--------------------------------+----------+----------+
| ``k4`` | double   | Fisheye distortion coeff. 4   | ``0.0``  | ``0.0``  |
+--------+----------+--------------------------------+----------+----------+

Image Parameters
---------------

+----------------+----------+--------------------------------+----------+----------+------------------+
| Parameter      | Type     | Description                    | Units    | Default  | Example          |
+================+==========+================================+==========+==========+==================+
| ``fps``       | double   | Frame rate of input images     | Hz       | -        | ``30.0``         |
+----------------+----------+--------------------------------+----------+----------+------------------+
| ``cols``      | unsigned | Image width                    | pixels   | -        | ``1241``         |
|               | int      |                                |          |          |                  |
+----------------+----------+--------------------------------+----------+----------+------------------+
| ``rows``      | unsigned | Image height                   | pixels   | -        | ``376``          |
|               | int      |                                |          |          |                  |
+----------------+----------+--------------------------------+----------+----------+------------------+
| ``color_order``| string  | Image colour format            | -        | -        | ``"Gray"``,      |
|               |          |                                |          |          | ``"RGB"``,       |
|               |          |                                |          |          | ``"RGBA"``,      |
|               |          |                                |          |          | ``"BGR"``,       |
|               |          |                                |          |          | ``"BGRA"``       |
+----------------+----------+--------------------------------+----------+----------+------------------+

Stereo/RGBD Parameters
---------------------

+----------------------+----------+--------------------------------+----------+----------+------------+
| Parameter            | Type     | Description                    | Units    | Default  | Example    |
+======================+==========+================================+==========+==========+============+
| ``focal_x_baseline`` | double   | Baseline × focal length       | pixels × | ``0.0``  | ``718.856``|
|                     |          | (stereo) or depth accuracy     | meters   |          |            |
|                     |          | parameter (RGBD)               |          |          |            |
+----------------------+----------+--------------------------------+----------+----------+------------+
| ``depth_threshold``  | double   | Depth threshold ratio for     | ratio    | ``40.0`` | ``40.0``   |
|                     |          | filtering                      |          |          |            |
+----------------------+----------+--------------------------------+----------+----------+------------+

System Configuration
===================

+------------------+----------+--------------------------------+----------+------------------+
| Parameter        | Type     | Description                    | Default  | Example          |
+==================+==========+================================+==========+==================+
| ``map_format``   | string   | Map storage format            | ``"msgpack"`` | ``"msgpack"``, |
|                 |          |                                |          | ``"sqlite3"``    |
+------------------+----------+--------------------------------+----------+------------------+
| ``num_grid_cols``| unsigned | Number of grid columns for    | ``64``   | ``96``           |
|                 | int      | spatial indexing               |          |                  |
+------------------+----------+--------------------------------+----------+------------------+
| ``num_grid_rows``| unsigned | Number of grid rows for       | ``48``   | ``48``           |
|                 | int      | spatial indexing               |          |                  |
+------------------+----------+--------------------------------+----------+------------------+

Feature Extraction Configuration
===============================

+----------------------+----------+--------------------------------+----------+----------------------------------+
| Parameter            | Type     | Description                    | Default  | Example                          |
+======================+==========+================================+==========+==================================+
| ``name``             | string   | Feature extraction model name | ``"default ORB feature extraction setting"`` | ``"custom ORB"`` |
+----------------------+----------+--------------------------------+----------+----------------------------------+
| ``scale_factor``     | float    | Scale factor for image pyramid| ``1.2``  | ``1.2``                          |
+----------------------+----------+--------------------------------+----------+----------------------------------+
| ``num_levels``       | unsigned | Number of pyramid levels      | ``8``    | ``8``                            |
|                     | int      |                                |          |                                  |
+----------------------+----------+--------------------------------+----------+----------------------------------+
| ``ini_fast_threshold``| unsigned| Initial FAST threshold        | ``20``   | ``20``                           |
|                     | int      |                                |          |                                  |
+----------------------+----------+--------------------------------+----------+----------------------------------+
| ``min_fast_threshold``| unsigned| Minimum FAST threshold        | ``7``    | ``7``                            |
|                     | int      |                                |          |                                  |
+----------------------+----------+--------------------------------+----------+----------------------------------+

Feature Extraction Pipeline
--------------------------

.. mermaid::

    flowchart TD
        A[Input Image] --> B[Create Image Pyramid]
        B --> C[Detect FAST Corners]
        C --> D[Compute ORB Descriptors]
        D --> E[Filter Features]
        E --> F[Output Keypoints & Descriptors]

Preprocessing Configuration
==========================

+------------------+----------+--------------------------------+----------+----------+
| Parameter        | Type     | Description                    | Default  | Example  |
+==================+==========+================================+==========+==========+
| ``min_size``     | unsigned | Minimum image size for        | ``800``  | ``800``  |
|                 | int      | processing                     |          |          |
+------------------+----------+--------------------------------+----------+----------+
| ``depthmap_factor``| double | Depth map scaling factor      | ``1.0``  | ``5000.0``|
+------------------+----------+--------------------------------+----------+----------+
| ``num_grid_cols``| unsigned | Grid columns for preprocessing | ``64``   | ``96``   |
|                 | int      |                                |          |          |
+------------------+----------+--------------------------------+----------+----------+
| ``num_grid_rows``| unsigned | Grid rows for preprocessing   | ``48``   | ``48``   |
|                 | int      |                                |          |          |
+------------------+----------+--------------------------------+----------+----------+
| ``mask_rectangles``| array  | Image mask rectangles         | ``[]``   | ``[[0.0, 1.0, 0.0, 0.1]]``|
+------------------+----------+--------------------------------+----------+----------+

Tracking Configuration
=====================

+----------------------------------+----------+--------------------------------+----------+------------------+
| Parameter                        | Type     | Description                    | Default  | Example          |
+==================================+==========+================================+==========+==================+
| ``backend``                      | string   | Optimization backend          | ``"g2o"`` | ``"g2o"``,     |
|                                 |          |                                |          | ``"gtsam"``      |
+----------------------------------+----------+--------------------------------+----------+------------------+
| ``reloc_distance_threshold``     | double   | Relocalization distance       | ``0.2``  | ``0.2``         |
|                                 |          | threshold                      |          |                  |
+----------------------------------+----------+--------------------------------+----------+------------------+
| ``reloc_angle_threshold``        | double   | Relocalization angle          | ``0.45`` | ``0.45``        |
|                                 |          | threshold                      |          |                  |
+----------------------------------+----------+--------------------------------+----------+------------------+
| ``init_retry_threshold_time``    | double   | Initialization retry threshold| ``5.0``  | ``5.0``         |
|                                 |          | time                           |          |                  |
+----------------------------------+----------+--------------------------------+----------+------------------+
| ``enable_auto_relocalization``   | bool     | Enable automatic              | ``true`` | ``true``         |
|                                 |          | relocalization                |          |                  |
+----------------------------------+----------+--------------------------------+----------+------------------+
| ``enable_temporal_keyframe_only_tracking``| bool | Enable temporal keyframe | ``false``| ``false``        |
|                                 |          | only tracking                 |          |                  |
+----------------------------------+----------+--------------------------------+----------+------------------+
| ``use_robust_matcher_for_relocalization_request``| bool | Use robust matcher | ``false``| ``false``        |
|                                 |          | for relocalization           |          |                  |
+----------------------------------+----------+--------------------------------+----------+------------------+
| ``max_num_local_keyfrms``       | unsigned | Maximum number of local       | ``60``   | ``60``           |
|                                 | int      | keyframes                     |          |                  |
+----------------------------------+----------+--------------------------------+----------+------------------+
| ``margin_local_map_projection`` | float    | Local map projection margin   | ``5.0``  | ``10.0``        |
+----------------------------------+----------+--------------------------------+----------+------------------+
| ``margin_local_map_projection_unstable``| float | Unstable local map      | ``20.0`` | ``20.0``        |
|                                 |          | projection margin            |          |                  |
+----------------------------------+----------+--------------------------------+----------+------------------+
| ``margin_last_frame_projection``| float    | Last frame projection margin  | ``20.0`` | ``20.0``        |
+----------------------------------+----------+--------------------------------+----------+------------------+

G2O Backend Parameters
---------------------

+----------------------+----------+--------------------------------+----------+----------+
| Parameter            | Type     | Description                    | Default  | Example  |
+======================+==========+================================+==========+==========+
| ``num_trials_robust``| unsigned | Number of robust trials       | ``2``    | ``2``    |
|                     | int      |                                |          |          |
+----------------------+----------+--------------------------------+----------+----------+
| ``num_trials``       | unsigned | Number of trials              | ``2``    | ``2``    |
|                     | int      |                                |          |          |
+----------------------+----------+--------------------------------+----------+----------+
| ``num_each_iter``    | unsigned | Number of iterations          | ``10``   | ``10``   |
|                     | int      |                                |          |          |
+----------------------+----------+--------------------------------+----------+----------+

GTSAM Backend Parameters
-----------------------

+------------------------+----------+--------------------------------+----------+----------+
| Parameter              | Type     | Description                    | Default  | Example  |
+========================+==========+================================+==========+==========+
| ``num_iter``           | unsigned | Number of iterations          | ``5``    | ``5``    |
|                       | int      |                                |          |          |
+------------------------+----------+--------------------------------+----------+----------+
| ``relative_error_tol`` | double   | Relative error tolerance      | ``1e-2`` | ``1e-2`` |
+------------------------+----------+--------------------------------+----------+----------+
| ``lambda_initial``     | double   | Initial lambda value          | ``1e-5`` | ``1e-5`` |
+------------------------+----------+--------------------------------+----------+----------+
| ``lambda_upper_bound`` | double   | Lambda upper bound            | ``1e-2`` | ``1e-2`` |
+------------------------+----------+--------------------------------+----------+----------+
| ``enable_outlier_elimination``| bool | Enable outlier elimination | ``true`` | ``true`` |
+------------------------+----------+--------------------------------+----------+----------+
| ``verbosity``          | string   | Verbosity level               | ``"SILENT"`` | ``"SILENT"`` |
+------------------------+----------+--------------------------------+----------+----------+

Mapping Configuration
====================

+----------------------------------+----------+--------------------------------+----------+------------------+
| Parameter                        | Type     | Description                    | Default  | Example          |
+==================================+==========+================================+==========+==================+
| ``backend``                      | string   | Optimization backend          | ``"g2o"`` | ``"g2o"``,     |
|                                 |          |                                |          | ``"gtsam"``      |
+----------------------------------+----------+--------------------------------+----------+------------------+
| ``baseline_dist_thr``            | double   | Baseline distance threshold   | ``1.0``  | ``0.07471049682``|
+----------------------------------+----------+--------------------------------+----------+------------------+
| ``baseline_dist_thr_ratio``      | double   | Baseline distance threshold   | ``0.02`` | ``0.02``         |
|                                 |          | ratio                          |          |                  |
+----------------------------------+----------+--------------------------------+----------+------------------+
| ``redundant_obs_ratio_thr``      | double   | Redundant observations ratio | ``0.9``  | ``0.95``         |
|                                 |          | threshold                      |          |                  |
+----------------------------------+----------+--------------------------------+----------+------------------+
| ``observed_ratio_thr``           | double   | Observed ratio threshold      | ``0.3``  | ``0.3``          |
+----------------------------------+----------+--------------------------------+----------+------------------+
| ``num_reliable_keyfrms``         | unsigned | Number of reliable keyframes  | ``2``    | ``2``            |
|                                 | int      |                                |          |                  |
+----------------------------------+----------+--------------------------------+----------+------------------+
| ``num_covisibilities_for_landmark_generation``| unsigned int | Covisibilities for | ``10`` | ``20``           |
|                                 |          | landmark generation            |          |                  |
+----------------------------------+----------+--------------------------------+----------+------------------+
| ``num_covisibilities_for_landmark_fusion``| unsigned int | Covisibilities for | ``10`` | ``20``           |
|                                 |          | landmark fusion                |          |                  |
+----------------------------------+----------+--------------------------------+----------+------------------+
| ``residual_deg_thr``             | float    | Residual degree threshold     | ``0.2``  | ``0.4``          |
+----------------------------------+----------+--------------------------------+----------+------------------+
| ``enable_interruption_of_landmark_generation``| bool | Enable landmark      | ``true`` | ``true``         |
|                                 |          | generation interruption       |          |                  |
+----------------------------------+----------+--------------------------------+----------+------------------+
| ``enable_interruption_before_local_BA``| bool | Enable interruption before | ``true`` | ``true``         |
|                                 |          | local BA                       |          |                  |
+----------------------------------+----------+--------------------------------+----------+------------------+
| ``erase_temporal_keyframes``     | bool     | Erase temporal keyframes      | ``false``| ``false``        |
+----------------------------------+----------+--------------------------------+----------+------------------+
| ``num_temporal_keyframes``       | unsigned | Number of temporal keyframes  | ``15``   | ``15``           |
|                                 | int      |                                |          |                  |
+----------------------------------+----------+--------------------------------+----------+------------------+
| ``top_n_covisibilities_to_search``| unsigned int | Top N covisibilities to | ``30`` | ``30``           |
|                                 |          | search                         |          |                  |
+----------------------------------+----------+--------------------------------+----------+------------------+

Loop Detection Configuration
===========================

+----------------------------------+----------+--------------------------------+----------+------------------+
| Parameter                        | Type     | Description                    | Default  | Example          |
+==================================+==========+================================+==========+==================+
| ``backend``                      | string   | Optimization backend          | ``"g2o"`` | ``"g2o"``,     |
|                                 |          |                                |          | ``"gtsam"``      |
+----------------------------------+----------+--------------------------------+----------+------------------+
| ``enabled``                      | bool     | Enable loop detection         | ``true`` | ``true``         |
+----------------------------------+----------+--------------------------------+----------+------------------+
| ``num_final_matches_threshold``  | unsigned | Final matches threshold       | ``40``   | ``40``           |
|                                 | int      |                                |          |                  |
+----------------------------------+----------+--------------------------------+----------+------------------+
| ``min_continuity``               | unsigned | Minimum continuity            | ``3``    | ``3``            |
|                                 | int      |                                |          |                  |
+----------------------------------+----------+--------------------------------+----------+------------------+
| ``reject_by_graph_distance``     | bool     | Reject by graph distance      | ``false``| ``true``         |
+----------------------------------+----------+--------------------------------+----------+------------------+
| ``min_distance_on_graph``        | unsigned | Minimum distance on graph     | ``50``   | ``50``           |
|                                 | int      |                                |          |                  |
+----------------------------------+----------+--------------------------------+----------+------------------+
| ``num_matches_thr``              | unsigned | Number of matches threshold   | ``20``   | ``20``           |
|                                 | int      |                                |          |                  |
+----------------------------------+----------+--------------------------------+----------+------------------+
| ``num_matches_thr_robust_matcher``| unsigned int | Robust matcher matches | ``0`` | ``0``               |
|                                 |          | threshold                      |          |                  |
+----------------------------------+----------+--------------------------------+----------+------------------+
| ``num_optimized_inliers_thr``    | unsigned | Optimized inliers threshold   | ``20``   | ``20``           |
|                                 | int      |                                |          |                  |
+----------------------------------+----------+--------------------------------+----------+------------------+
| ``top_n_covisibilities_to_search``| unsigned int | Top N covisibilities to | ``0`` | ``0``               |
|                                 |          | search                         |          |                  |
+----------------------------------+----------+--------------------------------+----------+------------------+
| ``use_fixed_seed``               | bool     | Use fixed seed                | ``false``| ``false``        |
+----------------------------------+----------+--------------------------------+----------+------------------+
| ``num_common_words_thr_ratio``   | float    | Common words threshold ratio | ``0.8``  | ``0.8``          |
+----------------------------------+----------+--------------------------------+----------+------------------+

Global Optimizer Configuration
=============================

+------------------------+----------+--------------------------------+----------+----------+
| Parameter              | Type     | Description                    | Default  | Example  |
+========================+==========+================================+==========+==========+
| ``thr_neighbor_keyfrms``| unsigned | Neighbor keyframes threshold | ``15``   | ``100``  |
|                       | int      |                                |          |          |
+------------------------+----------+--------------------------------+----------+----------+
| ``num_iter``           | unsigned | Number of iterations          | ``10``   | ``10``   |
|                       | int      |                                |          |          |
+------------------------+----------+--------------------------------+----------+----------+
| ``use_huber_kernel``   | bool     | Use Huber kernel              | ``false``| ``false``|
+------------------------+----------+--------------------------------+----------+----------+

Configuration Examples
=====================

KITTI Monocular
--------------

.. code-block:: yaml

    Camera:
      name: "KITTI monocular"
      setup: "monocular"
      model: "perspective"
      fx: 718.856
      fy: 718.856
      cx: 607.1928
      cy: 185.2157
      k1: 0.0
      k2: 0.0
      p1: 0.0
      p2: 0.0
      k3: 0.0
      fps: 10.0
      cols: 1241
      rows: 376
      color_order: "Gray"

    Feature:
      name: "default ORB feature extraction setting"
      scale_factor: 1.2
      num_levels: 8
      ini_fast_threshold: 20
      min_fast_threshold: 7

    Mapping:
      backend: "g2o"
      baseline_dist_thr_ratio: 0.02
      redundant_obs_ratio_thr: 0.9

    Tracking:
      backend: "g2o"

    System:
      map_format: "msgpack"
      num_grid_cols: 40
      num_grid_rows: 13

EuRoC Stereo
-----------

.. code-block:: yaml

    Camera:
      name: "EuRoC stereo"
      setup: "stereo"
      model: "perspective"
      fx: 435.2046959714599
      fy: 435.2046959714599
      cx: 367.4517211914062
      cy: 252.2008514404297
      k1: 0.0
      k2: 0.0
      p1: 0.0
      p2: 0.0
      k3: 0.0
      fps: 20.0
      cols: 752
      rows: 480
      focal_x_baseline: 47.90639384423901
      depth_threshold: 40
      color_order: "Gray"

    StereoRectifier:
      model: "perspective"
      K_left: [458.654, 0.0, 367.215, 0.0, 457.296, 248.375, 0.0, 0.0, 1.0]
      D_left: [-0.28340811, 0.07395907, 0.00019359, 1.76187114e-05, 0.0]
      K_right: [457.587, 0.0, 379.999, 0.0, 456.134, 255.238, 0.0, 0.0, 1]
      D_right: [-0.28368365, 0.07451284, -0.00010473, -3.555907e-05, 0.0]
      R_left: [0.999966347530033, -0.001422739138722922, 0.008079580483432283, ...]
      R_right: [0.9999633526194376, -0.003625811871560086, 0.007755443660172947, ...]

    Mapping:
      backend: "g2o"
      baseline_dist_thr: 0.11007784219
      redundant_obs_ratio_thr: 0.9
      erase_temporal_keyframes: false
      num_temporal_keyframes: 15

    Tracking:
      backend: "g2o"
      enable_temporal_keyframe_only_tracking: false
      margin_last_frame_projection: 10.0

    System:
      map_format: "msgpack"
      num_grid_cols: 47
      num_grid_rows: 30

TUM RGBD
--------

.. code-block:: yaml

    Camera:
      name: "TUM-RGBD RGBD"
      setup: "RGBD"
      model: "perspective"
      fx: 517.306408
      fy: 516.469215
      cx: 318.643040
      cy: 255.313989
      k1: 0.262383
      k2: -0.953104
      p1: -0.005358
      p2: 0.002628
      k3: 1.163314
      fps: 30.0
      cols: 640
      rows: 480
      focal_x_baseline: 40.0
      depth_threshold: 40.0
      color_order: "RGB"

    Preprocessing:
      min_size: 800
      depthmap_factor: 5000.0

    Mapping:
      baseline_dist_thr: 0.07471049682
      redundant_obs_ratio_thr: 0.9

    Tracking:
      margin_local_map_projection: 10.0

Equirectangular (360° Camera)
----------------------------

.. code-block:: yaml

    Camera:
      name: "RICOH THETA S 960"
      setup: "monocular"
      model: "equirectangular"
      fps: 30.0
      cols: 1920
      rows: 960
      color_order: "RGB"

    Preprocessing:
      min_size: 800
      mask_rectangles:
        - [0.0, 1.0, 0.0, 0.1]
        - [0.0, 1.0, 0.84, 1.0]
        - [0.0, 0.2, 0.7, 1.0]
        - [0.8, 1.0, 0.7, 1.0]

    Mapping:
      backend: "g2o"
      baseline_dist_thr_ratio: 0.02
      redundant_obs_ratio_thr: 0.95
      num_covisibilities_for_landmark_generation: 20
      num_covisibilities_for_landmark_fusion: 20
      residual_deg_thr: 0.4

    LoopDetector:
      backend: "g2o"
      enabled: true
      reject_by_graph_distance: true
      min_distance_on_graph: 50

    System:
      map_format: "msgpack"
      num_grid_cols: 96
      num_grid_rows: 48

Available Options
================

Map Storage Formats
------------------

- **msgpack**: Binary format for efficient storage (default)
- **sqlite3**: Database format for complex queries

Trajectory Export Formats
------------------------

- **KITTI**: KITTI dataset format (4x4 transformation matrix)
- **TUM**: TUM RGB-D dataset format (timestamp, translation, quaternion)

Optimization Backends
--------------------

- **g2o**: Default optimization backend (always available)
- **gtsam**: Alternative optimization backend (requires GTSAM compilation)

Camera Models
------------

- **perspective**: Standard pinhole camera model
- **fisheye**: Wide-angle fisheye lens model
- **equirectangular**: 360° panoramic camera model
- **radial_division**: Custom radial distortion model

Camera Setups
------------

- **monocular**: Single camera
- **stereo**: Two-camera setup
- **RGBD**: RGB + depth camera

Parameter Tuning Guidelines
==========================

Performance vs Accuracy
----------------------

- **High Performance**: Reduce ``num_levels``, increase ``scale_factor``, lower thresholds
- **High Accuracy**: Increase ``num_levels``, decrease ``scale_factor``, higher thresholds

Memory Usage
-----------

- **Low Memory**: Reduce ``num_grid_cols/rows``, lower ``num_temporal_keyframes``
- **High Memory**: Increase grid size, more temporal keyframes

Robustness
----------

- **High Robustness**: Increase ``min_num_valid_pts``, lower ``reprojection_error_threshold``
- **Fast Tracking**: Decrease thresholds, enable ``enable_temporal_keyframe_only_tracking``

Loop Detection
-------------

- **Aggressive**: Lower ``min_distance_on_graph``, higher ``num_matches_thr``
- **Conservative**: Higher ``min_distance_on_graph``, lower ``num_matches_thr``

Configuration Validation
=======================

The system includes configuration validation to ensure parameters are within acceptable ranges:

.. mermaid::

    flowchart TD
        A[Load YAML File] --> B[Parse Configuration]
        B --> C[Validate Camera Parameters]
        C --> D{Camera Valid?}
        D -->|Yes| E[Validate Feature Parameters]
        D -->|No| F[Report Camera Errors]
        E --> G{Feature Valid?}
        G -->|Yes| H[Validate Tracking Parameters]
        G -->|No| I[Report Feature Errors]
        H --> J{Tracking Valid?}
        J -->|Yes| K[Validate Mapping Parameters]
        J -->|No| L[Report Tracking Errors]
        K --> M{Mapping Valid?}
        M -->|Yes| N[Validate Loop Detection Parameters]
        M -->|No| O[Report Mapping Errors]
        N --> P{Loop Detection Valid?}
        P -->|Yes| Q[Configuration Complete]
        P -->|No| R[Report Loop Detection Errors]

Advanced Configuration
=====================

Compilation-Time Options
-----------------------

.. code-block:: bash

    # Enable GTSAM backend
    cmake -DUSE_GTSAM=ON ..

    # Enable ArUco marker detection
    cmake -DUSE_ARUCO=ON ..

    # Enable ArUco Nano
    cmake -DUSE_ARUCO_NANO=ON ..

    # Enable OpenMP
    cmake -DUSE_OPENMP=ON ..

    # Enable SSE optimizations
    cmake -DUSE_SSE_ORB=ON -DUSE_SSE_FP_MATH=ON ..

    # Choose BOW framework
    cmake -DBOW_FRAMEWORK=DBoW2 ..

Runtime Configuration
--------------------

.. code-block:: cpp

    // Save map in different formats
    slam.save_map_database("map.msg");  // MessagePack format
    slam.save_map_database("map.db");   // SQLite format

    // Save trajectories in different formats
    slam.save_frame_trajectory("trajectory.txt", "TUM");
    slam.save_keyframe_trajectory("keyframe_trajectory.txt", "KITTI");

Configuration Best Practices
===========================

1. **Start with Defaults**: Begin with default parameters and tune gradually
2. **Camera Calibration**: Ensure accurate camera calibration for best results
3. **Environment Considerations**: Adjust parameters based on environment characteristics
4. **Performance Monitoring**: Monitor system performance and adjust accordingly
5. **Validation**: Test configurations on representative datasets
6. **Documentation**: Document custom configurations for reproducibility 