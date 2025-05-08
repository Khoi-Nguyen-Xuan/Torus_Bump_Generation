# Torus Generation with continuous variation options 

The script is located in `torus_generation.ipynb`, provided in Jupyter Notebook format. I recommended to run this notebook in local IDE, because some required libraries cannot be installed in Google Colab. 

The notebook is structured into three main sections:

1. **Utility Functions** – 4 helper functions used throughout the script.  
2. **Interactive Torus Generation** – The interactive torus meshes generation.  
3. **File Export** – A script to export generated torus meshes as `.ply` files.

Although the script includes extensive comments, I will brief the key steps behind the torus generation with a bump moving around.

### Step 1: 3D Grid Generation

We begin by generating a coordinate array with 100 points uniformly spaced from -1 to 1 for each axis. This array is the coordinate space over which the signed distance function (SDF) is evaluated. Then we use this coordinate to create a 3D mesh 

```python
coords = np.linspace(-1, 1, 100)
x = np.stack(np.meshgrid(coords, coords, coords))
```

The grid will look something like this:

![image](https://github.com/user-attachments/assets/135be419-9007-47ac-b898-f8ff52e421a9)






