# Developer Documentation

This section contains comprehensive developer documentation for Stella VSLAM, incorporating AI-generated content with enhanced visualisations using PlantUML diagrams.

## Documentation Structure

### System Overview (`system_overview.rst`)
- **Purpose**: High-level understanding of VSLAM concepts and system capabilities
- **Content**: 
  - VSLAM fundamentals and core concepts
  - System capabilities and supported camera configurations
  - Algorithm overview and performance characteristics
  - Use cases and comparison with other SLAM systems
- **Target Audience**: New developers, researchers, and users getting started with VSLAM

### Architecture (`architecture.rst`)
- **Purpose**: Detailed system architecture and design principles
- **Content**:
  - High-level system architecture with PlantUML diagrams
  - Multi-threaded processing flow
  - Data flow patterns and component interactions
  - Camera model architecture and database schema
  - System state machine and design principles
- **Target Audience**: System architects, developers working on core components

### Components (`components.rst`)
- **Purpose**: Detailed breakdown of all system components
- **Content**:
  - Core system components (System, Tracking, Mapping, Global Optimisation)
  - Data management components (Map Database, BoW Database, Camera Database)
  - Feature processing components
  - Publishing and I/O components
  - Configuration and utilities
  - Component interaction patterns and lifecycle
- **Target Audience**: Developers implementing or extending system components

### Configuration Guide (`configuration_guide.rst`)
- **Purpose**: Comprehensive configuration reference and examples
- **Content**:
  - All system parameters with detailed descriptions
  - Configuration examples for different camera types
  - Parameter tuning guidelines and best practices
  - Advanced configuration options
- **Target Audience**: Developers configuring the system for specific use cases

## Key Features

### PlantUML Diagrams
All documentation includes PlantUML diagrams for:
- System architecture overview
- Data flow patterns
- Component relationships
- Threading models
- State machines
- Database schemas
- Configuration hierarchy

### Code Examples
Comprehensive code examples for:
- System initialisation
- Frame processing
- Configuration setup
- Custom implementations
- Integration patterns

### Configuration Templates
Ready-to-use configuration templates for:
- Perspective cameras
- Fisheye cameras
- Equirectangular cameras
- Different performance profiles

## Building the Documentation

### Prerequisites
1. Install PlantUML:
   ```bash
   sudo apt-get install plantuml
   ```

2. Install Python dependencies:
   ```bash
   pip install -r requirements.txt
   ```

### Building
```bash
cd docs
make html
```

The built documentation will be available in `_build/html/`.

## Integration with AI-Generated Content

This documentation incorporates content from the `ai-overview` folder, which contains:
- `Overview.md`: System overview and concepts
- `Architecture.md`: Detailed architecture information
- `Components.md`: Component breakdown and interfaces
- `Config.md`: Comprehensive configuration reference

The AI-generated content has been:
- Converted to RST format for Sphinx
- Enhanced with PlantUML diagrams
- Organised into logical sections
- Integrated with existing documentation structure

## Contributing

When contributing to the developer documentation:

1. **Use PlantUML**: Create diagrams using PlantUML syntax for consistency
2. **Follow RST Format**: Use proper RST formatting and cross-references
3. **Include Examples**: Provide practical code examples and configuration templates
4. **Update Index**: Ensure new pages are added to the appropriate toctree in `index.rst`

## Related Resources

- **Official Documentation**: [stella-cv.readthedocs.io](https://stella-cv.readthedocs.io/)
- **GitHub Repository**: [stella-cv/stella_vslam](https://github.com/stella-cv/stella_vslam)
- **ROS Wrapper**: [stella-cv/stella_vslam_ros](https://github.com/stella-cv/stella_vslam_ros)
- **Community Discussions**: [GitHub Discussions](https://github.com/stella-cv/stella_vslam/discussions) 