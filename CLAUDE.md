# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is an R project that creates 3D population density "spike" maps of Benin using the rayshader package. The project downloads population data from the Kontur Population dataset and renders high-quality 3D visualizations showing population density as vertical spikes.

## Package Management

This project uses **renv** for reproducible package management:

- `renv::restore()` - Install all required packages from renv.lock
- `renv::snapshot()` - Update renv.lock with current package versions
- `renv::status()` - Check if packages are in sync with renv.lock

The `.Rprofile` automatically activates the renv environment when the project loads.

## Main Script Architecture

The core workflow is in `benin-spike-map.R`:

1. **Data Download** (`get_population_data()` - lines 32-45):
   - Downloads Kontur population data for Benin (BJ country code)
   - Handles .gz extraction automatically
   - Skips re-downloading if files exist

2. **Data Loading** (`get_population_data()` - lines 58-65):
   - Loads .gpkg file using sf
   - Transforms to WGS84 coordinate system (LONGLAT projection)

3. **Raster Conversion** (lines 77-117):
   - Calculates optimal raster dimensions based on geographic bounds
   - Converts vector data to raster using stars::st_rasterize()
   - Creates matrix for rayshader processing

4. **3D Rendering** (lines 130-161):
   - Uses rayshader to create 3D visualization
   - Renders high-quality PNG output

## Key Dependencies

- **rayshader**: 3D data visualization and rendering
- **sf**: Spatial data handling and geometric operations
- **stars**: Raster data processing and rasterization
- **tidyverse**: Data manipulation (dplyr, ggplot2, etc.)
- **httr**: HTTP requests for data download
- **R.utils**: File compression utilities

## Running the Project

To execute the complete visualization pipeline:

```r
source("benin-spike-map.R")
```

This will:
1. Install missing packages automatically
2. Download population data (if not present)
3. Process data and generate 3D visualization
4. Output final PNG image

## Data Sources

- **Kontur Population Dataset**: https://data.humdata.org/dataset/kontur-population-dataset
- **File Format**: GeoPackage (.gpkg) compressed with gzip
- **Country**: Benin (ISO code: BJ)
- **Date**: 2022-06-30 snapshot

## Customization Options

To adapt for different countries:
- Change country code in URL (line 29): Replace "BJ" with desired ISO country code
- Update file naming accordingly (line 30)

To adjust visualization:
- **Size**: Modify `size` variable (line 102) for resolution
- **Colors**: Update `cols` array (lines 123-126) for color scheme
- **Camera**: Adjust `phi`, `theta`, `zoom` parameters (lines 141-143, 148)
- **Lighting**: Modify `lightdirection`, `lightaltitude`, `lightintensity` (lines 155-157)

## Output Files

- `benin-population.gpkg.gz`: Downloaded compressed data
- `benin-population.gpkg`: Extracted population data
- `benin_population_2022_2.png`: Final 3D visualization
- `benin-population-spike-map.jpg`: Example output image

## Development Notes

- The project uses Lambert projection initially but switches to LONGLAT for better results with non-European countries
- The rasterization process automatically calculates optimal dimensions to maintain aspect ratio
- 3D rendering can be computationally intensive; adjust `size` parameter for faster iterations during development