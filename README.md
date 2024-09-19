# Indian_Driver_Behaviour_data_analysis

This project processes GNSS, velocity, and image data to create a color-coded map based on vehicle speed. The map also includes image popups that correspond to timestamps in the data. The project uses GNSS data to plot latitude and longitude points, velocity data to calculate speed, and Basler image data to attach relevant images to each point on the map.

Table of Contents
  Requirements
  Project Structure
  Data Processing Workflow
  Output
  Usage
  License

## Requirements
Ensure you have the following packages installed before running the script:


    pip install pandas folium matplotlib
The script processes data from multiple CSV files and uses images stored locally. Make sure you have the following files:

bestpos.csv: Contains GNSS (latitude, longitude) data.
bestvel.csv: Contains velocity data (horizontal speed).
basler.csv: Contains timestamps of images.
A directory containing images: /path/to/images where images are named in the format image_%Y-%m-%d-%H-%M-%S.%f.jpg.

## Project Structure

    .
    ├── bestpos.csv            # GNSS Data: latitude, longitude, and time
    ├── bestvel.csv            # BestVel Data: horizontal speed and time
    ├── basler.csv             # Basler camera data: time of images
    ├── /images         # Directory of images captured by the Basler camera
    └── main_script.py         # Main Python script to process and merge data


## Data Processing Workflow
The workflow of the script involves several key steps, from reading raw data files to producing a final visual representation of the GNSS data, vehicle speed, and corresponding images. Below is an expanded explanation of each part of the workflow:

### 1. Loading and Processing GNSS Data (bestpos.csv)
The script begins by loading the GNSS data from a CSV file (bestpos.csv), which contains time, latitude, and longitude fields.
The pandas library is used to read the CSV file, and the relevant columns '(time, .lat, and .lon)' are extracted and stored in lists.
A DataFrame is created using this data. This GNSS DataFrame serves as the foundation for positioning the vehicle’s location on the map.
### Example columns in bestpos.csv:

time: Timestamp of the GNSS data.
  .lat: Latitude value.
  .lon: Longitude value.
The latitude and longitude values will later be used to plot the vehicle's location on a map.

### 2. Loading and Processing Velocity Data (bestvel.csv)
The second step involves reading the velocity data from another CSV file (bestvel.csv), which contains time and horizontal speed (hor_speed).
The script multiplies each horizontal speed value by 3.6 to convert the speed from meters per second (m/s) to kilometers per hour (km/h).
A new DataFrame is created with the converted speed values, which will later be used for color-coding the data points on the map.
Example columns in bestvel.csv:

time: Timestamp of the velocity data.
.hor_speed: Horizontal speed in m/s (converted to km/h in the script).
### 3. Loading and Processing Basler Camera Data (basler.csv)
The script then reads the Basler camera data (basler.csv), which contains timestamps of images captured during the vehicle’s journey.
The Basler data is processed to extract only the time column. The extracted time values are used to match each GNSS data point with the corresponding image captured around the same time.
A DataFrame is created for the Basler data, which will later be merged with the GNSS and velocity data.
Example columns in basler.csv:

time: Timestamps corresponding to the captured images.
### 4. Merging Data (GNSS, BestVel, and Basler Data)
The script uses the pandas.merge_asof() function to merge the GNSS, velocity, and Basler camera data based on the nearest timestamp. This ensures that each GNSS point is matched with the nearest velocity and image data.
This merged DataFrame contains:
Latitude and longitude from GNSS data.
Horizontal speed from velocity data.
Timestamped image filenames from Basler data.
Merged DataFrame columns:

time: Timestamp.
latitude, longitude: GNSS coordinates.
horizontal_speed: Speed in km/h.
image_filename: Filename of the corresponding Basler image.
### 5. Image Association
The script searches through the /baleno_images directory to find the nearest image filename based on the timestamp.
The image filenames follow a specific naming pattern ( 'image_%Y-%m-%d-%H-%M-%S.%f.jpg' ), and the script uses the timestamp to match the images to GNSS data points.
If an image is found for a particular GNSS point, the filename is added to the merged DataFrame.
### 6. Creating the Map with Folium
Once the data is merged, the script creates a visual map using the folium library. The map is centered around the mean latitude and longitude values from the GNSS data.
Each GNSS point is plotted as a CircleMarker on the map, with the color of the marker determined by the vehicle's speed at that location. The color is computed using a color gradient from red (slow) to green (fast).
The popup for each marker includes:
Latitude and longitude.
Speed (km/h).
If available, an image corresponding to that timestamp is also included in the popup.
### 7. Saving the Map as an HTML File
After creating the map with all GNSS points and image popups, the script saves the map as an interactive HTML file (img_test6.html).
This HTML file can be opened in any web browser, allowing the user to explore the map, click on individual GNSS points, and view the associated speed and images.
### 8. Creating and Saving a Color Bar
A color bar is generated to visually represent the speed range using matplotlib. The color bar helps users understand the relationship between marker colors and the speed of the vehicle.
The color bar is saved as an image (colorbar6.png) and can be used as a reference when viewing the map.


The output of this script is a highly interactive and visual representation of GNSS data, vehicle speed, and corresponding images. Here's a more detailed breakdown of each output component:

### 1. Interactive Map (HTML File)
    File name: img_test6.html

Description: The primary output of the script is an interactive map generated using the folium library. This map displays the vehicle's path by plotting markers for each GNSS data point on a satellite map background from Google Maps.

#### Features:

Map Centering: The map is centered on the mean latitude and longitude from the GNSS dataset, ensuring that the vehicle's path is clearly visible.
Marker Visualization: Each GNSS point is represented by a CircleMarker. The markers are color-coded based on the vehicle's horizontal speed. The color gradient is a visual cue, with:
Red for slower speeds.
Green for faster speeds.
#### Popups:
Each marker includes a popup with detailed information. The popup shows:
Latitude and Longitude values of the GNSS point.
Speed at that point in km/h.
#### Image: 
If a corresponding image from the Basler camera is available, the popup includes a thumbnail of the image, providing a visual context for the location at that time.
This makes the map highly interactive, allowing the user to explore both the path and the visual surroundings at each GNSS point.
Use Cases:

Navigation Tracking: The map can be used to visualize vehicle movement over time and examine how speed changes along the route.
Image Verification: The images provide a visual snapshot of the vehicle's surroundings at various points, making it useful for tasks like environmental analysis or road inspection.
### 2. Color Bar (PNG Image)
File name: colorbar6.png
Description: This is a color bar image created using matplotlib. It serves as a legend for the map, showing the mapping between marker colors and vehicle speeds.
#### Features:
The color bar ranges from red to green, indicating slow to fast speeds, respectively. The horizontal color bar includes speed values (in km/h) to help interpret the color coding on the map.
It helps users quickly understand how the vehicle's speed changes along the route by looking at the marker colors on the map.
### 3. Data Files (CSV)
Processed CSV files are generated for further analysis or reuse in other applications.
#### File names:
gnss_data.csv: Contains GNSS data with time, latitude, and longitude columns.
bestvel_data.csv: Contains velocity data with time and horizontal_speed (in km/h).
basler_data.csv: Contains image timestamps from the Basler camera.
These CSV files can be used independently for tasks such as:
Further data analysis (e.g., trajectory analysis, speed pattern analysis).
Reuse in other GIS applications or mapping software.
### 4. Merged DataFrame
Although not explicitly saved as a file, the script generates a merged DataFrame in memory. This DataFrame combines the GNSS, velocity, and image data, with the following columns:
time: Timestamp of the data point.
latitude and longitude: GNSS coordinates.
horizontal_speed: Vehicle speed at that time (in km/h).
image_filename: Filename of the associated image.
Utility: The merged data is useful for detailed time-based analysis and can be saved to a CSV or used for plotting or modeling tasks.
### 5. Console Output
During the execution of the script, several outputs are printed to the console to provide feedback and diagnostic information:
Speed values: The script prints the first few converted speed values (in km/h) to the console for verification.
Merged DataFrame: A preview of the first few rows of the merged DataFrame is printed, showing how the GNSS, velocity, and image data have been combined.
File paths: The script prints the paths of the generated files (HTML map, colorbar.png) to confirm their successful creation.
Practical Applications and Use Cases:
### Vehicle Route Visualization:

The script provides a way to visually track and analyze vehicle movement, including speed changes, using GNSS and velocity data over time.
The route can be examined on a map, providing insights into driving patterns and environmental contexts with the help of Basler images.
### Fleet Management and Navigation Analysis:

This can be used for managing vehicle fleets by providing real-time or historical route tracking and performance metrics based on speed.
### Autonomous Vehicle Testing:

During the testing of autonomous or semi-autonomous vehicles, this visualization allows the assessment of vehicle behavior along the route, including its response to speed limits and road conditions (as captured by the Basler images).
### Research and Development:

The combination of map, speed data, and visual context provides researchers with tools to study the performance of navigation algorithms, analyze speed patterns, and correlate visual data with GNSS and velocity readings.
### Environmental and Road Condition Monitoring:

By linking GNSS data with images, the output can be used to study road conditions or environmental factors, such as road quality, traffic density, or weather conditions, at different points on the route.
## Summary
The script processes GNSS, velocity, and image data to produce an interactive map that combines geographic information, speed, and visual context. The output allows for in-depth analysis of vehicle movement with clear, color-coded visual markers and relevant images. The additional CSV files and color bar offer tools for further data exploration and analysis. This comprehensive output is useful for navigation tracking, fleet management, vehicle testing, and research applications.

## License
This project is licensed under the MIT License.

