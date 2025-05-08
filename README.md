# Torus Generation with continuous variations

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


### Step 2: Base Torus Creation with SDF

We generate the base torus (without the bump) using SDF implementation from [Inigo Quilez](https://iquilezles.org/articles/distfunctions/).  
The SDF is then converted into a mesh using the `marching_cubes` algorithm from `skimage`.

The SDF formula explanation is commented in the **Utilities** section of the notebook.

```python
sdf = sdf_torus(x, radius, thickness)  #Get SDF 
verts, faces, normals, values = measure.marching_cubes(sdf, level=0)
```

The torus base will look something like this:

![image](https://github.com/user-attachments/assets/6b6b4754-d4d9-4e91-b6de-c73eee623ca7)









