# Data-VisualizAR
Visualize 3D models (.obj) or data using augmented reality on your mobile phone through the web with [AR.js](https://github.com/jeromeetienne/AR.js)

- :iphone: Open this link on your phone [https://tinyurl.com/web-ar-obj](https://pearsonkyle.github.io/Data-VisualizAR/static/github.html)

- :camera: Point camera at picture below 

![](static/patterns/pattern-earth.png)

## Web server
```python 
python web_ar.py
``` 

URL Paths: 
```
/
/model
/video
/models/<name>
```

## Coming Soon
AR & VR visualizations of planetary collisions from SPH simulations 

### [Web-AR](https://github.com/jeromeetienne/AR.js)

![](static/videos/sph_visualization.gif)

### [Web-VR](https://aframe.io/)
![](static/videos/sph_web_vr.gif)

### [Unity](https://unity.com/)
![](static/videos/sph_unity_vr.gif)

## Converting [Paraview](https://www.paraview.org/) Models with [Blender](https://www.blender.org/) (.x3d -> .obj)

1. Open Blender (v2.79)
2. Navigate to script editor (crtl+right arrow)x3
3. Open new script. Press button "+ New"
4. Delete the square model, if present
5. Paste code below 
```python
import bpy
import glob 

file_loc = "C:\\Users\\Kyle\\Documents\\SPH_objects\\"
files = glob.glob(file_loc+"*.x3d")
for i in range(len(files)):

    imported_object = bpy.ops.import_scene.x3d(filepath=files[i])
    obj = bpy.context.selected_objects[0]
    exported_object = bpy.ops.export_scene.obj(filepath=files[i].split('.x3d')[0]+'.obj')

    print('Imported name: ', obj.name)
    bpy.data.objects[obj.name].select = True   
    bpy.ops.object.delete()
    
```
Make sure to change the file location

## Creating an animation from multiple obj files in Blender 
```python
# https://docs.blender.org/api/2.79b/info_quickstart.html
import bpy
import glob 

file_loc = "C:\\Users\\Kyle\\Documents\\Unity Projects\\SPH_Visualization\\Assets\\models\\"
files = glob.glob(file_loc+"*.x3d")

scene = bpy.context.scene
scene.objects.keys()
bpy.context.scene.render.fps = 5
bpy.context.scene.frame_start = 0
bpy.context.scene.frame_end = len(files)

objs = [] 
for i in range(len(files)):
    imported_object = bpy.ops.import_scene.x3d(filepath=files[i])
    obj = bpy.context.selected_objects[0]
    obj.hide = True
    modifier=obj.modifiers.new('Decimate 0.5','DECIMATE')
    modifier.ratio=0.5
    modifier.use_collapse_triangulate=True
    objs.append( obj)

def hide_true(objs,idx):
    for i in range(len(objs)):
        if i == idx:
            objs[i].hide = False
            objs[i].keyframe_insert(data_path="hide")
        else:
            objs[i].hide = True
            objs[i].keyframe_insert(data_path="hide")

for i in range(len(files)):
    scene.frame_set(i)    
    hide_true(objs,i)

for k in range(len(bpy.data.objects)-1,0,-1):
    if 'Face' not in bpy.data.objects[k].name:
        bpy.data.objects[k].select = True
    else: 
        bpy.data.objects[k].select = False 

bpy.ops.object.delete()
```

![](static/videos/blender_anim.gif)