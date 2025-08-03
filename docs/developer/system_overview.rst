.. _chapter-developer-system-overview:

==================
System Overview
==================

What is VSLAM?
==============

Visual Simultaneous Localisation and Mapping (VSLAM) is a technology that enables a system to simultaneously build a map of an unknown environment and determine its own position within that map using visual information from cameras.

Core Concepts
=============

SLAM Process
-----------

1. **Tracking**: Estimate camera pose for each incoming frame
2. **Mapping**: Build a sparse 3D map of the environment
3. **Loop Detection**: Identify when the camera returns to a previously visited location
4. **Optimisation**: Refine the map and trajectory using bundle adjustment

Key Components
--------------

Feature-based Approach
~~~~~~~~~~~~~~~~~~~~~~

- Uses ORB (Oriented FAST and Rotated BRIEF) features for robust matching
- Extracts features at multiple scales for scale invariance
- Maintains a vocabulary tree for efficient feature matching

Multi-threaded Architecture
~~~~~~~~~~~~~~~~~~~~~~~~~~~

- **Tracking Thread**: Real-time pose estimation
- **Mapping Thread**: Background map construction and refinement
- **Global Optimisation Thread**: Loop closure and bundle adjustment

Camera Model Support
~~~~~~~~~~~~~~~~~~~

- **Perspective**: Standard pinhole camera model
- **Fisheye**: Wide-angle fisheye lens support
- **Equirectangular**: 360° panoramic camera support
- **Custom**: Extensible framework for new camera models

System Capabilities
==================

Supported Camera Configurations
------------------------------

- **Monocular**: Single camera localisation and mapping
- **Stereo**: Two-camera setup with known baseline
- **RGBD**: RGB + depth camera (e.g., Kinect, RealSense)

Map Management
-------------

- **Online Mapping**: Real-time map construction
- **Map Storage**: Save maps to disk in various formats
- **Map Loading**: Load pre-built maps for localisation
- **Localisation Mode**: Track pose using existing maps

Performance Features
-------------------

- **Real-time Operation**: 30+ FPS tracking capability
- **Loop Closure**: Automatic detection and correction of trajectory loops
- **Robust Tracking**: Handles occlusions, lighting changes, and motion blur
- **Scalable Maps**: Efficient handling of large environments

Algorithm Overview
==================

Feature Extraction and Matching
------------------------------

1. **ORB Feature Detection**: Fast corner detection with orientation
2. **Feature Description**: Binary descriptors for efficient matching
3. **Vocabulary Matching**: Hierarchical feature matching using BoW (Bag of Words)

Pose Estimation
--------------

1. **Initialisation**: Two-view geometry for initial pose estimation
2. **Tracking**: Feature-based pose tracking with motion model
3. **Relocalisation**: Recovery from tracking failures

Map Construction
---------------

1. **Keyframe Selection**: Intelligent selection of frames for mapping
2. **Triangulation**: 3D point reconstruction from feature correspondences
3. **Bundle Adjustment**: Non-linear optimisation of camera poses and 3D points

Loop Detection and Closure
-------------------------

1. **Place Recognition**: Identifying previously visited locations
2. **Loop Verification**: Geometric verification of loop candidates
3. **Pose Graph Optimisation**: Global trajectory refinement

System Architecture Overview
===========================

The Stella VSLAM system follows a modular, multi-threaded architecture:

.. mermaid::

    graph TB
        subgraph "Stella VSLAM System"
            subgraph "Core Modules"
                TM[Tracking Module]
                MM[Mapping Module]
                GOM[Global Optimisation Module]
            end
            
            subgraph "Data Management"
                DB[(Map Database)]
                BOW[(BoW Database)]
                CAM[(Camera Database)]
            end
            
            subgraph "Processing"
                FE[Feature Extraction]
                CM[Camera Models]
                IO[I/O Modules]
            end
            
            subgraph "Publishing"
                MP[Map Publisher]
                FP[Frame Publisher]
            end
        end
        
        TM --> DB
        MM --> DB
        GOM --> DB
        TM --> BOW
        TM --> CAM
        FE --> TM
        CM --> TM
        IO --> DB
        MP --> DB
        FP --> TM

Multi-threaded Processing Flow
=============================

The system utilises multiple threads for optimal performance:

.. mermaid::

    graph LR
        subgraph "Main Thread"
            UI[User Interface]
            CF[Camera Feed]
        end
        
        subgraph "Tracking Thread"
            TF[Frame Tracking]
            TI[Initialisation]
            TR[Relocalisation]
        end
        
        subgraph "Mapping Thread"
            KF[Keyframe Insertion]
            LM[Local Mapping]
            BA[Bundle Adjustment]
        end
        
        subgraph "Global Optimisation Thread"
            LD[Loop Detection]
            LC[Loop Closure]
            PGO[Pose Graph Optimisation]
        end
        
        UI --> CF
        CF --> TF
        TF --> KF
        KF --> LD
        LD --> LC
        LC --> PGO
        PGO --> TF

Use Cases
=========

Research Applications
--------------------

- **Robotics**: Autonomous navigation and localisation
- **Augmented Reality**: Camera tracking for AR applications
- **Computer Vision**: 3D reconstruction and scene understanding

Commercial Applications
----------------------

- **Autonomous Vehicles**: Localisation in urban environments
- **Drones**: Navigation and mapping in GPS-denied environments
- **Mobile Devices**: Indoor navigation and AR experiences

Performance Characteristics
==========================

Accuracy
--------

- **Monocular**: ~1-3% trajectory error (depending on environment)
- **Stereo**: ~0.5-1% trajectory error
- **RGBD**: ~0.1-0.5% trajectory error

Computational Requirements
-------------------------

- **CPU**: Multi-core processor recommended
- **Memory**: 2-8GB RAM depending on map size
- **Storage**: Minimal for real-time operation, larger for map storage

Real-time Performance
--------------------

- **Tracking**: 30+ FPS on modern hardware
- **Mapping**: Background processing with minimal impact on tracking
- **Loop Detection**: Asynchronous processing

Comparison with Other SLAM Systems
==================================

Advantages
----------

- **Modular Design**: Easy to understand and modify
- **Multi-camera Support**: Flexible camera model framework
- **Map Persistence**: Save and reuse maps
- **Active Development**: Community-driven improvements

Limitations
-----------

- **Feature-based**: Requires textured environments
- **Sparse Maps**: Produces point clouds rather than dense reconstructions
- **Computational Cost**: Higher than some lightweight alternatives

Development Workflow
===================

For developers working with Stella VSLAM, the typical workflow involves:

1. **System Setup**: Configure camera parameters and system settings
2. **Initialisation**: Start the SLAM system with appropriate configuration
3. **Frame Processing**: Feed camera frames to the system
4. **Pose Retrieval**: Extract camera poses and map data
5. **Map Management**: Save and load maps as needed
6. **Optimisation**: Tune parameters for specific use cases

Integration Patterns
===================

The system provides several integration patterns:

- **Direct API**: C++ interface for direct integration
- **ROS Integration**: ROS wrapper for robotics applications
- **Python Bindings**: Python interface for rapid prototyping
- **Standalone Applications**: Example applications for testing and validation 