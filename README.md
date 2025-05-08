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

<img src="https://github.com/user-attachments/assets/135be419-9007-47ac-b898-f8ff52e421a9" alt="Base Torus" width="400"/>


### Step 2: Base Torus Creation with SDF

We generate the base torus (without the bump) using SDF implementation from [Inigo Quilez](https://iquilezles.org/articles/distfunctions/).  
The SDF is then converted into a mesh using the `marching_cubes` algorithm from `skimage`.

The SDF formula explanation is commented in the **Utilities** section of the notebook.

```python
sdf = sdf_torus(x, radius, thickness)  #Get SDF 
verts, faces, normals, values = measure.marching_cubes(sdf, level=0)
```

The torus base will look something like this:

<img src="https://github.com/user-attachments/assets/6b6b4754-d4d9-4e91-b6de-c73eee623ca7" alt="Base Torus" width="400"/>


### Step 3: Noise field creation

To add some irregularities to the surface, we generate a 3D gradient noise field. This noise perturbs the spatial grid, introducing surface distortion before applying any bumps. 

```python
x_warp = gradient_noise(x, noise_scale, noise_strength, seed)
```


### Step 4:  Bump field creation (Gaussian bump)

A localized "bump" is created on the torus surface by using a 3D Gaussian function. The center of the bump moves along a circular path (the torus tube) based on a angle parameter.

- bump_angle :the angular position of the bump along the torus.
- gaussian_center : the center of the bump.
- x_dist :the distance from each point in the grid to the bump center.
- x_bump :a smooth, localized elevation using the Gaussian formula.

```python 
angle = np.pi * bump_angle
gaussian_center = np.array([np.sin(angle), 0., np.cos(angle)]) * radius
x_dist = np.linalg.norm((x - gaussian_center[:, None, None, None]), axis=0)
x_bump = bump_height * np.exp(-1. / bump_width * x_dist**2)
```


### Step 5: Deform the Torus Surface

The Gaussian bump is merged into the noise field by computing its gradient and adding it to the warp field.  This gradient acts like a directional force, pushing the surface outward from the bump center. 
- The gradient of x_bump produces a 3D vector field pointing away from the bump center 
- This gradient is added to the noise field x_warp to form the complete displacement field
- The field is interpolated at the mesh vertex positions using interpn
  
```python
# Add bump gradient to warp field
x_warp += -np.stack(np.gradient(x_bump))

# Interpolate displacement at mesh vertices
x_warp = rearrange(x_warp, 'v h w d -> h w d v')
vertex_noise = interpn([np.arange(100)] * 3, x_warp, verts, bounds_error=False, fill_value=0)
vertex_noise = np.nan_to_num(vertex_noise)

# Apply the deformation
warped_verts = verts + vertex_noise
```

The final torus will have a bump looking like this:

<img width="659" alt="Screenshot 2025-05-08 at 4 57 12 PM" src="https://github.com/user-attachments/assets/49ee560a-dbe6-44ec-b795-2c2ae427501c" />








