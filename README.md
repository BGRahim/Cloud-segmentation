# 3D Point Cloud Segmentation (sphere + planes)

This program reads a .pcd point cloud, finds ONE sphere and several planes
using my own RANSAC code, and saves a colored .ply file.

## Install
    pip install -r requirements.txt

(The code really only uses numpy. OpenCV is allowed but not needed.
No PCL / Open3D etc. The .pcd reader and .ply writer are written by me.)

## Run
    python3 main.py <input_file.pcd> <output_file.ply>

Example:
    python3 main.py Frame4994.pcd result_Frame4994.ply

It is fully automatic, there is nothing to type or tune while it runs.

## Colors
- sphere: yellow (255, 255, 0)
- every plane: its own different color
- points that belong to nothing (noise): grey

## How it works (short)
1. Read x, y, z from the PCD (other columns like rgb/intensity are ignored).
   Points with NaN or (0,0,0) are removed (no depth measured).
2. RANSAC for planes: pick 3 random points, make a plane, count the points
   closer than a threshold, repeat, keep the best, refine with SVD. The big
   planes are found first and removed.
3. RANSAC for the sphere in the remaining points: pick 4 random points,
   solve a small linear system for center and radius, count inliers, keep the best.
   (Planes go first because a very big sphere looks flat and could be confused with a wall.)
4. Smaller planes are searched in what is left. We stop when the best plane
   is too small.
5. The thresholds are not fixed numbers, they are a fraction of the size of
   the scene (diagonal of the bounding box), so it works for other files too.

## Results folder
`results/` has 4 example outputs (Frame4994, Frame9387, Frame15439, Frame5551).
