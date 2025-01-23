# 🌐 Tightly Packed Hexagonal Grid Over the USA
This project generates a tightly packed hexagonal grid over the USA's bounding box using Python, Shapely, GeoPandas, and Matplotlib. The hexagonal grid uses alternating column offsets for a compact layout. The grid cells are saved as Shapely polygons and can be exported as GeoJSON or shapefiles for GIS purposes.

## 📂 Code Overview
The code consists of two main functions:

create_hex_grid(bounds, hex_size)
Generates the hexagonal grid within the given bounding box using a specified hexagon size.

create_hexagon(x, y, hex_size)
Creates an individual hexagon centered at given coordinates (x, y).

The bounding box used for this example represents the continental USA.

## 📜 Prerequisites
To run this script, make sure you have the following libraries installed:

bash
Copy
Edit
pip install numpy matplotlib shapely geopandas
## 🚀 How to Use
### Run the Code
Copy and run the Python script in your environment.

### Adjust Hexagon Size
Modify the hex_size parameter to change the radius of hexagons.

### Save as GeoJSON/Shapefile (Optional)
Uncomment the lines at the bottom of the script to export the grid as GeoJSON or Shapefile.

## 📊 Example Output
Below is a visualization of the generated hexagonal grid over the USA:


## 🧩 Code Snippet
python
Copy
Edit
import numpy as np
import matplotlib.pyplot as plt
from shapely.geometry import Polygon, box
import geopandas as gpd

### Function to create hexagonal grid cells with column-based adjustments
def create_hex_grid(bounds, hex_size):
    """
    Create a tightly packed hexagonal grid with alternating half-offset columns.
    
    :param bounds: (min_lon, min_lat, max_lon, max_lat) - Bounding box of the area
    :param hex_size: Size of each hexagon (radius in degrees)
    :return: A list of Shapely polygons representing the hexagonal grid cells
    """
    min_lon, min_lat, max_lon, max_lat = bounds
    hexagons = []
    
    # Calculate the distances for a tightly packed grid
    dx = 1.5 * hex_size  # Horizontal distance between hexagon centers
    dy = np.sqrt(3) * hex_size  # Vertical distance between hexagon centers

    # Generate hexagons column by column
    x = min_lon
    column = 0  # Track column number for staggering
    while x <= max_lon + hex_size:  # Extend slightly beyond bounds to cover edges
        y = min_lat
        if column % 2 == 1:  # Odd columns
            y += dy / 2  # Offset by half the height of a hexagon
        while y <= max_lat + hex_size:
            # Create a hexagon centered at (x, y)
            hexagon = create_hexagon(x, y, hex_size)
            if hexagon.intersects(box(min_lon, min_lat, max_lon, max_lat)):
                hexagons.append(hexagon)
            y += dy
        x += dx
        column += 1
    
    return hexagons

### Function to create a single hexagon centered at (x, y)
def create_hexagon(x, y, hex_size):
    """
    Create a hexagon centered at (x, y).
    """
    angle = np.linspace(0, 2 * np.pi, 7)
    coords = [(x + hex_size * np.cos(a), y + hex_size * np.sin(a)) for a in angle]
    return Polygon(coords)

### Define USA Bounding Box (latitude, longitude)
bounds = (-125.0, 24.396308, -66.93457, 49.384358)

### Hexagon radius (distance from center to a vertex)
hex_size = 1

### Create the hexagonal grid
hex_grid = create_hex_grid(bounds, hex_size)

### Convert the grid to a GeoDataFrame for plotting
gdf = gpd.GeoDataFrame(geometry=hex_grid, crs="EPSG:4326")

### Plot the hexagonal grid over the USA
ax = gdf.plot(figsize=(10, 15), edgecolor='black', alpha=0.5)
ax.set_title("Tightly Packed Hexagonal Grid of USA (Column Adjustment)")
ax.set_xlim([-125, -66])
ax.set_ylim([24, 49])
plt.show()

 ***Optionally***: Save the grid to a shapefile or GeoJSON
##### gdf.to_file("usa_hex_grid.shp")
##### gdf.to_file("usa_hex_grid.geojson", driver='GeoJSON')
## 📂 Output Files
Shapefile: usa_hex_grid.shp
GeoJSON: usa_hex_grid.geojson
You can load these files into GIS software like QGIS or ArcGIS for further analysis.
