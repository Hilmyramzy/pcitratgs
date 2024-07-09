NAMA KELOMPOK

1. NADYA KHAIRUNNISA - 312210133

2. DHEA DWI ADELIA - 312210116
              
3. HILMY SYADDAD RAMZY - 312210162
              
4. IHSAN HADIMULYA - 312210047

MATA KULIAH : PENGOLAHAN CITRA

DOSEN PENGAMPU : MUHAMMAD FACTAN S.Kom., M.Kom., MTCNA.

======================================================================

[Link Google Drive](https://drive.google.com/drive/folders/1b7G6bOvKIhmDcLUpFxWsTuQP9oUtK3ym?usp=drive_link)
              
```import streamlit as st
import numpy as np
import matplotlib.pyplot as plt
import cv2
from PIL import Image

# Function to perform k-means clustering and display results
def process_image(image_path, k):
    # Reading the image
    image = cv2.imread(image_path)

    # Checking if the image was read successfully
    if image is None:
        st.error(f"Image not found at path: {image_path}")
        return

    # Extracting image properties
    image_height, image_width, num_channels = image.shape
    image_size = image.size
    image_dtype = image.dtype

    # Example DPI values (you can replace these with actual values if known)
    horizontal_resolution = 72  # DPI (Dots Per Inch)
    vertical_resolution = 72    # DPI (Dots Per Inch)

    # Convert color from BGR to RGB
    image = cv2.cvtColor(image, cv2.COLOR_BGR2RGB)

    # Display the original image and its properties
    st.image(image, caption='Original Image', use_column_width=True)

    details = (f"Dimensions: {image_width} x {image_height}\n"
               f"Width: {image_width} pixels\n"
               f"Height: {image_height} pixels\n"
               f"Number of Channels: {num_channels}\n"
               f"Total Number of Pixels: {image_size}\n"
               f"Data Type: {image_dtype}\n"
               f"Horizontal Resolution: {horizontal_resolution} DPI\n"
               f"Vertical Resolution: {vertical_resolution} DPI")

    st.text(details)

    # Extracting pixel values and reshaping to 2D
    pixel_vals = image.reshape((-1, 3))

    # Convert pixel values to float32 for k-means
    pixel_vals = np.float32(pixel_vals)

    # Criteria for stopping the k-means algorithm
    criteria = (cv2.TERM_CRITERIA_EPS + cv2.TERM_CRITERIA_MAX_ITER, 100, 0.85)

    # Performing k-means clustering
    retval, labels, centers = cv2.kmeans(pixel_vals, k, None, criteria, 10, cv2.KMEANS_RANDOM_CENTERS)

    # Convert cluster centers to 8-bit values
    centers = np.uint8(centers)
    segmented_data = centers[labels.flatten()]

    # Reshape the data back to the original image shape
    segmented_image = segmented_data.reshape((image.shape))

    # Display the segmented image
    st.image(segmented_image, caption=f'Segmented Image (K={k})', use_column_width=True)

    # Display color patches for each cluster center in a single row with multiple columns
    st.write("Cluster Colors:")
    
    cols = st.columns(4)
    for i, center in enumerate(centers):
        with cols[i % 4]:
            # Create a color patch for each cluster center
            color_patch = np.zeros((50, 50, 3), dtype=np.uint8)
            color_patch[:, :, :] = center

            # Convert color patch to Image for display
            color_patch_img = Image.fromarray(color_patch)

            # Display the color patch and its RGB values
            st.image(color_patch_img, caption=f'Color: {center}', use_column_width=False)

# Streamlit app layout
st.title('Image Segmentation with K-Means Clustering')

# Upload image files
uploaded_files = st.file_uploader("Choose images", type=["jpg", "jpeg", "png"], accept_multiple_files=True)

# Input for number of clusters
k = st.number_input("Enter the number of clusters (k):", min_value=1, max_value=20, value=3)

# Process each uploaded image
if uploaded_files:
    for uploaded_file in uploaded_files:
        # Save the uploaded file to a temporary location
        with open(f"temp_{uploaded_file.name}", "wb") as f:
            f.write(uploaded_file.getbuffer())
        
        # Process the image
        process_image(f"temp_{uploaded_file.name}", k)```
