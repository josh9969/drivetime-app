import streamlit as st
import openrouteservice
from shapely.geometry import Point, LineString
import geopandas as gpd
import matplotlib.pyplot as plt
import contextily as ctx

API_KEY = '5b3ce3597851110001cf62483c9fa348736d4315a694410fd874e918'
client = openrouteservice.Client(key=API_KEY)

def get_drive_time_and_route(origin, destination):
    try:
        route = client.directions(
            coordinates=[origin, destination],
            profile='driving-car',
            format='geojson'
        )
        duration = route['features'][0]['properties']['segments'][0]['duration']
        geometry = route['features'][0]['geometry']
        return duration, geometry
    except Exception as e:
        st.error(f"Error: {e}")
        return None, None

def main():
    st.title('Drive Time Calculator')

    st.sidebar.header('Input Coordinates')
    origin_lat = st.sidebar.number_input("Origin Latitude", value=25.003067569091343)
    origin_lon = st.sidebar.number_input("Origin Longitude", value=55.16747261201182)
    dest_lat = st.sidebar.number_input("Destination Latitude", value=25.25714576061916)
    dest_lon = st.sidebar.number_input("Destination Longitude", value=55.29771919667428)

    if st.sidebar.button('Calculate Drive Time'):
        origin = [origin_lon, origin_lat]
        destination = [dest_lon, dest_lat]
        duration, geometry = get_drive_time_and_route(origin, destination)

        if duration and geometry:
            st.write(f"Drive time is approximately {duration / 60:.2f} minutes.")
            
            # Create GeoDataFrame for the route
            line = LineString(geometry['coordinates'])
            gdf_route = gpd.GeoDataFrame(geometry=[line], crs='EPSG:4326')
            
            # Create GeoDataFrame for the points
            gdf_points = gpd.GeoDataFrame(geometry=[Point(origin), Point(destination)], crs='EPSG:4326')
            
            # Plot the map
            fig, ax = plt.subplots(figsize=(10, 10))
            gdf_route.plot(ax=ax, color='blue', alpha=0.5, edgecolor='k')
            gdf_points.plot(ax=ax, color='red', markersize=100)
            ctx.add_basemap(ax, source=ctx.providers.OpenStreetMap.Mapnik, crs='EPSG:4326')
            
            ax.set_title('Drive Route')
            ax.set_xlabel('Longitude')
            ax.set_ylabel('Latitude')

            st.pyplot(fig)

if __name__ == "__main__":
    main()
