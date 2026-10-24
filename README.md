
# Robot Path Planning Using Constrained Delaunay Triangulation

**Python | Computational Geometry | OpenCV | Shapely | NetworkX | Dijkstra's Algorithm**

A Python-based path-planning pipeline that combines computer vision, constrained Delaunay triangulation (CDT), and graph algorithms to compute obstacle-aware routes through classroom environments.

**Authors:** Nadezhda Dominguez Salinas & Keshawn Boughton  
**Macalester College | Computational Geometry | Fall 2025**

## Overview

How can computational geometry help a robot navigate an environment filled with obstacles?

In this project, we developed a pipeline that takes an image of a classroom, identifies obstacles, represents the navigable space geometrically, and computes a route between two points without crossing the modeled obstacles.

Our approach combines five main steps:

1. **Obstacle Detection:** Use OpenCV to detect classroom objects and approximate them with convex hulls.
2. **Environment Construction:** Represent the classroom as a polygon with interior holes corresponding to obstacles.
3. **Constrained Delaunay Triangulation:** Partition the navigable space into triangles while respecting obstacle boundaries.
4. **Navigation Graph Construction:** Connect adjacent triangle centroids to form a weighted graph.
5. **Shortest-Path Computation:** Apply Dijkstra's algorithm to find a minimum-distance route through the navigation graph.

The resulting path is visualized over the classroom environment.

## Final Result

<p align="center">
  <img src="path_progression/shortest_path_graph.png" width="650" alt="Computed shortest path through the classroom navigation graph">
</p>

*Computed route between selected points using Dijkstra's algorithm on the centroid navigation graph.*

In one classroom example, the pipeline generated:

| Metric | Result |
|---|---|
| Triangles in the CDT | 168 |
| Centroids along the computed route | 17 |
| Pathfinding algorithm | Dijkstra's algorithm |
| Graph edge weights | Euclidean distance |
| Environment | Classroom with polygonal obstacles |

The computed route is shortest within the constructed navigation graph, rather than necessarily being the globally shortest route through continuous space.

## Contributions

This project was developed collaboratively by Nadezhda Dominguez Salinas and Keshawn Boughton.

### My Primary Contributions — Nadezhda Dominguez Salinas

I primarily implemented the computational geometry and path-planning components:

- Implemented constrained Delaunay triangulation using Shapely to partition the navigable classroom environment.
- Constructed a weighted navigation graph using triangle centroids and adjacency relationships in NetworkX.
- Implemented obstacle-intersection checks to prevent graph connections from crossing obstacle boundaries.
- Applied Dijkstra's shortest-path algorithm to compute a route between selected positions.
- Developed visualizations of the triangulation, navigation graph, and resulting path.

### Keshawn Boughton's Primary Contributions

Keshawn primarily developed the classroom image-processing pipeline:

- Implemented obstacle detection using OpenCV and Canny edge detection.
- Developed contour-processing and convex hull methods to approximate classroom obstacles as polygons.
- Prepared the geometric obstacle representations used by the path-planning pipeline.

We collaborated on integrating the components, debugging, and completing the final research project.

## How It Works

### 1. Obstacle Detection

The first step is to extract obstacles from an image of the classroom.

Using OpenCV, the image is processed with Canny edge detection to identify object boundaries. Convex hulls are then computed to approximate the shapes of tables, chairs, and desks.

These obstacles are subsequently represented as polygonal holes within the classroom boundary.

<p align="center">
  <img src="sample_classrooms/circle_classroom.png" width="300" alt="Original classroom environment">
  <img src="path_progression/circle_classroom_hulls.png" width="300" alt="Detected classroom obstacles represented as convex hulls">
</p>

*Original classroom image (left) and extracted obstacle convex hulls (right).*

### 2. Polygonal Environment Construction

The classroom is modeled as a rectangular polygon with dimensions corresponding to the input image.

The obstacle convex hulls are inserted as interior holes, producing a polygonal region representing the available navigable space.

This geometric representation allows subsequent algorithms to distinguish traversable areas from obstacles.

<p align="center">
  <img src="path_progression/classroom_setup.png" width="500" alt="Classroom polygon with obstacle holes">
</p>

*Polygonal representation of the classroom and its obstacles.*

### 3. Constrained Delaunay Triangulation

Using Shapely, we compute a constrained Delaunay triangulation of the navigable classroom environment.

The triangulation partitions the available space into triangles while respecting the polygonal obstacle boundaries.

This creates a geometric structure that can be converted into a navigation graph for pathfinding.

<p align="center">
  <img src="path_progression/constrained_delaunay_triangulation.png" width="550" alt="Constrained Delaunay triangulation of classroom environment">
</p>

*Constrained Delaunay triangulation of the navigable classroom space.*

### 4. Centroid Computation and Navigation Graph Construction

For each triangle in the CDT, we compute its centroid and represent it as a node in a NetworkX graph.

Two centroid nodes are connected when their corresponding triangles are adjacent and the connecting segment does not cross an obstacle.

Each edge is weighted by the Euclidean distance between the centroids.

The resulting weighted graph represents possible routes through the classroom.

<p align="center">
  <img src="path_progression/centroids_CDT.png" width="300" alt="Centroids of triangles in the constrained Delaunay triangulation">
  <img src="path_progression/centroid_graph.png" width="300" alt="Weighted navigation graph connecting adjacent triangle centroids">
</p>

*Triangle centroids (left) and the constructed navigation graph (right).*

### 5. Shortest-Path Computation

The starting and destination positions are associated with their nearest triangle centroids.

Dijkstra's algorithm is then applied to the weighted centroid graph to find a minimum-distance route between these nodes.

Because graph connections are restricted to valid navigable segments, the resulting route avoids crossing the modeled obstacle polygons.

<p align="center">
  <img src="path_progression/shortest_path_graph.png" width="550" alt="Shortest path computed using Dijkstra's algorithm">
</p>

*Final obstacle-aware route computed using Dijkstra's algorithm.*

## Technologies

| Technology | Purpose |
|---|---|
| Python | Main implementation language |
| OpenCV | Image processing and obstacle detection |
| Shapely | Polygon operations and constrained Delaunay triangulation |
| NetworkX | Navigation graph construction and shortest-path computation |
| NumPy | Numerical operations |
| Matplotlib | Visualization of the geometric environment and computed routes |

## Installation and Usage

### 1. Clone the Repository

```bash
git clone https://github.com/Nadizdom34/robot-path-planning-cdt.git
cd robot-path-planning-cdt
```

### 2. Install Dependencies

Install the required Python libraries:

```bash
pip install numpy opencv-python "shapely>=2.1" matplotlib networkx
```

Shapely 2.1 or later is required for its constrained Delaunay triangulation functionality.

### 3. Select a Classroom Environment

The repository includes sample classroom environments in the `sample_classrooms/` directory.

The default input image is:

`sample_classrooms/circle_classroom.png`

To use a different environment:

1. Add the new classroom image to `sample_classrooms/`.
2. Open `ConvexHullObstacles.py`.
3. Update the image path in the `cv2.imread()` call to reference the new image.

### 4. Run Obstacle Detection

Run:

```bash
python ConvexHullObstacles.py
```

This script processes the classroom image and computes the convex hull representations of the obstacles.

### 5. Run the Path-Planning Pipeline

Run:

```bash
python CDTPath.py
```

This script constructs the geometric environment, computes the triangulation, builds the navigation graph, and finds the shortest route.

### 6. View the Results

The resulting visualizations are stored in the `path_progression/` directory.

These include the obstacle representation, polygonal environment, triangulation, centroid graph, and final computed route.

## Challenges and Lessons Learned

### Converting Geometry into a Navigation Graph

A key challenge was transforming the triangulated environment into a graph that preserved the geometric constraints.

This required identifying adjacent triangles, computing their centroids, assigning distance-based edge weights, and ensuring that graph connections did not intersect obstacles.

### Integrating Computer Vision and Geometry

The image-processing pipeline and geometric path-planning algorithms needed compatible representations of the environment.

Representing detected objects as convex polygons made it possible to integrate the obstacle-detection results into the triangulation and navigation graph.

### Handling Image-Processing Limitations

We explored image thresholding but found that lighting and glare could interfere with obstacle detection.

Canny edge detection and convex hull extraction provided a more useful way to approximate obstacle boundaries.

## Limitations and Future Work

The project demonstrates path planning in a modeled classroom environment. Although the resulting routes were computed and visualized in Python, they were not executed on a physical robot.

Potential future improvements include:

- Accounting for robot dimensions and required obstacle clearance.
- Incorporating turning constraints and movement costs.
- Supporting dynamic obstacles and changing environments.
- Comparing Dijkstra's algorithm with alternative pathfinding methods.
- Testing the computed routes on physical robot hardware.

## Conclusion

This project demonstrates how computer vision, computational geometry, and graph algorithms can be integrated into an obstacle-aware path-planning pipeline.

By representing classroom obstacles as polygons, triangulating the navigable space, constructing a weighted centroid graph, and applying Dijkstra's algorithm, we computed a route through the modeled environment.

The project provided hands-on experience implementing geometric algorithms, designing graph representations, and integrating multiple Python libraries into a complete computational pipeline.


## Project Documentation

For additional details about the mathematical background, implementation, and results:

- **[Final Research Report](final_report.pdf)** — Full explanation of our methodology, implementation, and findings.
- **[Project Presentation](presentation_computational_geometry.pdf)** — Visual walkthrough of the path-planning pipeline and results.

## References

1. Gene Eu Jan, Chien-Hung Lin, and Chih-Yung Hsu.  
   **Obstacle avoidance path planning based on Delaunay triangulation for mobile robots.**  
   *IEEE Access*, 9:137894–137905, 2021.

2. Gillies, S., et al.  
   **Shapely: Manipulation and Analysis of Geometric Objects, Constrained Delaunay Triangulation.**  
   Python package, version 2.1.0.  
   https://shapely.readthedocs.io/

## Authors

**Nadezhda Dominguez Salinas**  
**Keshawn Boughton**

Macalester College — Computational Geometry, Fall 2025

