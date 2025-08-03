.. _chapter-developer-architecture:

==============
Architecture
==============

High-Level Architecture
======================

The Stella VSLAM system is built around a modular, multi-threaded architecture designed for real-time performance and extensibility.

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

Multi-Threaded Architecture
==========================

The system utilises a multi-threaded approach to achieve real-time performance while maintaining accuracy:

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

Data Flow Architecture
=====================

The data flow through the system follows a well-defined pipeline:

.. mermaid::

    flowchart TD
        A[Camera Input] --> B[Frame Creation]
        B --> C[Feature Extraction]
        C --> D[Tracking Module]
        D --> E{Tracking Success?}
        E -->|Yes| F[Pose Estimation]
        E -->|No| G[Relocalisation]
        G --> D
        F --> H[Keyframe Decision]
        H -->|Yes| I[Keyframe Insertion]
        H -->|No| J[Update Motion Model]
        I --> K[Local Mapping]
        K --> L[Bundle Adjustment]
        L --> M[Loop Detection]
        M -->|Loop Found| N[Loop Closure]
        M -->|No Loop| O[Continue]
        N --> P[Global Optimisation]
        P --> Q[Map Update]
        Q --> R[Pose Refinement]
        R --> D
        O --> D
        J --> D

Component Interaction
====================

The interaction between system components follows this sequence:

.. mermaid::

    sequenceDiagram
        participant User
        participant System
        participant Tracking
        participant Mapping
        participant GlobalOpt
        participant Database
        
        User->>System: Start SLAM
        System->>Tracking: Initialise
        System->>Mapping: Start mapping thread
        System->>GlobalOpt: Start optimisation thread
        
        loop Frame Processing
            User->>System: Feed frame
            System->>Tracking: Process frame
            Tracking->>Database: Query landmarks
            Tracking->>Tracking: Estimate pose
            
            alt New keyframe needed
                Tracking->>Mapping: Insert keyframe
                Mapping->>Database: Store keyframe
                Mapping->>Mapping: Local bundle adjustment
            end
            
            alt Loop detected
                Mapping->>GlobalOpt: Request loop closure
                GlobalOpt->>Database: Optimise pose graph
                GlobalOpt->>Tracking: Update poses
            end
        end
        
        User->>System: Save map
        System->>Database: Export map data

Camera Model Architecture
========================

The system supports multiple camera models through an extensible architecture:

.. mermaid::

    classDiagram
        class CameraBase {
            <<abstract>>
            +project(points_3d)
            +unproject(points_2d)
            +get_camera_matrix()
        }
        
        class PerspectiveCamera {
            +fx, fy: focal lengths
            +cx, cy: principal point
            +distortion coefficients
        }
        
        class FisheyeCamera {
            +fx, fy: focal lengths
            +cx, cy: principal point
            +k1, k2, k3, k4: fisheye coefficients
        }
        
        class EquirectangularCamera {
            +cols, rows: image dimensions
            +fps: frame rate
        }
        
        class RadialDivisionCamera {
            +fx, fy: focal lengths
            +cx, cy: principal point
            +division coefficients
        }
        
        CameraBase <|-- PerspectiveCamera
        CameraBase <|-- FisheyeCamera
        CameraBase <|-- EquirectangularCamera
        CameraBase <|-- RadialDivisionCamera

Database Schema
==============

The system uses a relational database structure for efficient data management:

.. mermaid::

    erDiagram
        KEYFRAME {
            unsigned_int id PK
            Mat44_t pose_cw
            timestamp timestamp
            vector landmarks
            vector observations
        }
        
        LANDMARK {
            unsigned_int id PK
            Vec3_t pos_w
            vector observations
            bool is_observable
        }
        
        OBSERVATION {
            unsigned_int keyframe_id FK
            unsigned_int landmark_id FK
            Vec2_t pos_2d
            float scale_level
        }
        
        CAMERA {
            unsigned_int id PK
            string name
            string model
            json parameters
        }
        
        KEYFRAME ||--o{ OBSERVATION : "has"
        LANDMARK ||--o{ OBSERVATION : "observed_by"
        CAMERA ||--o{ KEYFRAME : "captured_by"

Module Dependencies
==================

The system modules have well-defined dependencies:

.. mermaid::

    graph TD
        subgraph "Core System"
            SYS[System]
            CFG[Config]
        end
        
        subgraph "Tracking"
            TRK[Tracking Module]
            INIT[Initializer]
            REL[Relocalizer]
            KFI[Keyframe Inserter]
        end
        
        subgraph "Mapping"
            MAP[Mapping Module]
            LMC[Local Map Cleaner]
            LMU[Local Map Updater]
        end
        
        subgraph "Optimisation"
            GOM[Global Optimisation]
            LBA[Loop Bundle Adjuster]
            LD[Loop Detector]
        end
        
        subgraph "Data"
            MDB[Map Database]
            BDB[BoW Database]
            CDB[Camera Database]
        end
        
        subgraph "Features"
            FE[Feature Extraction]
            ORB[ORB Extractor]
        end
        
        SYS --> CFG
        SYS --> TRK
        SYS --> MAP
        SYS --> GOM
        
        TRK --> INIT
        TRK --> REL
        TRK --> KFI
        TRK --> MDB
        TRK --> BDB
        
        MAP --> LMC
        MAP --> LMU
        MAP --> MDB
        
        GOM --> LBA
        GOM --> LD
        GOM --> MDB
        
        TRK --> FE
        FE --> ORB

System State Machine
===================

The system operates through well-defined states:

.. mermaid::

    stateDiagram-v2
        [*] --> Initialising
        Initialising --> Tracking : Initialisation Success
        Initialising --> Lost : Initialisation Failed
        
        Tracking --> Tracking : Continue Tracking
        Tracking --> Lost : Tracking Failed
        Tracking --> Mapping : New Keyframe
        
        Lost --> Tracking : Relocalisation Success
        Lost --> Lost : Relocalisation Failed
        
        Mapping --> Tracking : Mapping Complete
        Mapping --> GlobalOptimisation : Loop Detected
        
        GlobalOptimisation --> Tracking : Optimisation Complete
        
        Tracking --> [*] : System Shutdown
        Lost --> [*] : System Shutdown
        Mapping --> [*] : System Shutdown
        GlobalOptimisation --> [*] : System Shutdown

Key Design Principles
====================

Modularity
----------

- Each component is designed as a separate module with clear interfaces
- Components can be easily replaced or extended
- Dependencies are minimised and well-defined

Real-time Performance
--------------------

- Multi-threading separates time-critical operations from background processing
- Tracking runs in the main thread for minimal latency
- Mapping and optimisation run in background threads

Extensibility
------------

- Camera models can be easily extended by implementing the base interface
- New feature extractors can be added through the feature extraction framework
- Optimisation backends can be swapped (g2o, GTSAM)

Robustness
----------

- Multiple fallback mechanisms for tracking failures
- Robust outlier rejection in all optimisation steps
- Graceful degradation when resources are limited

Memory Efficiency
----------------

- Spatial indexing for efficient landmark queries
- Intelligent keyframe selection to limit memory usage
- Efficient data structures for real-time operation

Threading Model
==============

The system employs a sophisticated threading model to balance performance and accuracy:

Main Thread
-----------

- **Purpose**: User interface and frame input
- **Responsibilities**: Camera feed processing, user interaction
- **Performance**: Must maintain real-time frame rates

Tracking Thread
--------------

- **Purpose**: Real-time pose estimation
- **Responsibilities**: Feature matching, pose estimation, keyframe decisions
- **Performance**: Critical for real-time operation

Mapping Thread
-------------

- **Purpose**: Background map construction
- **Responsibilities**: Keyframe processing, local bundle adjustment
- **Performance**: Can run at lower priority

Global Optimisation Thread
-------------------------

- **Purpose**: Loop detection and global optimisation
- **Responsibilities**: Place recognition, pose graph optimisation
- **Performance**: Asynchronous, non-blocking

Data Flow Patterns
=================

The system implements several data flow patterns:

Producer-Consumer Pattern
------------------------

- **Tracking Thread**: Produces keyframes
- **Mapping Thread**: Consumes keyframes for processing
- **Synchronisation**: Thread-safe queues with proper locking

Observer Pattern
---------------

- **Map Updates**: Components observe map changes
- **Pose Updates**: Tracking observes optimisation results
- **Event-driven**: Asynchronous notification system

Factory Pattern
--------------

- **Camera Models**: Factory creates appropriate camera instances
- **Feature Extractors**: Factory creates feature extraction components
- **Configuration**: Factory creates configured components

Performance Optimisations
========================

Spatial Indexing
---------------

- Grid-based spatial indexing for landmark queries
- Configurable grid resolution for performance tuning
- Efficient nearest-neighbour searches

Memory Management
----------------

- Smart pointer usage for automatic memory management
- Object pooling for frequently allocated objects
- Lazy loading of map data

Parallel Processing
------------------

- OpenMP support for parallel feature extraction
- SIMD instructions for vector operations
- GPU acceleration for certain operations (if available)

Caching Strategies
-----------------

- Feature descriptor caching
- Camera model parameter caching
- Map data caching for frequently accessed regions 