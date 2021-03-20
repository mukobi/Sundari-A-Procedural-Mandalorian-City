# Sundari: A Procedural Mandalorian City

## Intro

_Please check out https://www.artstation.com/artwork/d8WqRx for a prettier presentation of this project!_

I procedurally modeled Sundari, the capital city of Mandalore, from "Star Wars: The Clone Wars" in Houdini as an exercise in procedural modeling large sci-fi environments for film/TV/games. Because Houdini's Mantra renderer is so slow, I also turned it into my first real-time cinematic in Unreal Engine. Sundari was a natural choice because it already appears as if large chunks of it were procedurally generated (search the internet for "Mandalore city" to see some image examples) and because I'm a huge Star Wars fan!

I used 4 different tools to build this work: Houdini took care of the procedural generation and model scattering of the city, Blender let me quickly model a library of assets to be scattered and handled some intermediate processing, Quixel Mixer created textures for the city metal and a mask for the blocky lights on the glass, and Unreal Engine 4 shaded, lit, animated, and rendered everything. 


## Folder Structure

- Blender: Project files and exported models for modelling the car and scattered city pieces along with exported FBX version of the the OBJ models exported from Houdini.
- Final Output: Images, GIFs, and videos showing my progress and selected rendered works. If you just want to see my results, please check out this folder.
- HIP: Houdini project files. This was where I also exported OBJs from Houdini, but I've .gitignored that output folder.
- Quixel Mixer: Textures I exported from Quixel Mixer for the metal and glass of the city.
- SundariUE4: Unreal Engine 4.26 project bringing everything together and adding lighting, the flying car particle systems, camera animation, and rendering for the final cinematic.

Note: Some of the larger models exceeded GitHub's 100MB limit and aren't included, so you'll need to reexport them from Houdini and import them into Unreal if you wish to fully recreate my work. Check out the .gitignore file to see the specific files that aren't included.


## Details

Here are a few details of note explaining my workflow to complete this project:

### Pipeline

Because I was using the free Houdini Apprentice, the only format I could export from it was Wavefront OBJ. This format is extremely limiting as it only stores geometry (vertices, vertex normals, vertex UV coordinates, and face lists), but I managed to pull off a janky workaround that barely worked for this project: For each separate material and part of the city, I exported a separate model (e.g. the glass of the small blocks) which let me assign them a unique material in UE4.

Rather than UV unwrapping and texturing each of these chunked models (each with ~1-50 million triangles), I wrote a random value per connected piece into all of said piece's UVs that I could later use as a source of randomness in a shader. For example, one block might have UV=(0.12, 0.78) for all its vertices while the flat panel next to it might have UV=(0.77, 0.66). Then, in UE4, I used world-aligned textures (essentially tri-planar projection) to project a texture onto all the models, but I scaled the texture based on the random value from the UVs of each connected piece. For the tiny emissive lights all around, I used the UV u-component to randomly choose a color and the v-component to vary its emissive intensity.

### Blender

I used Blender for several things. For one, I modeled a kit of various boxes that Houdini would randomly choose from and scatter around the city, including 5 5x2x2 tall blocks, 7 .2x1x1 flat panels, and 4 1x1x1 small cubes. I cut out different patterns from each variation to be the glass. Because I exported OBJs with my random UVs from Houdini, I also used Blender to import those OBJs, generate a second UV set for lightmap coordinates, and export as FBX. With some of the really big models (&gt;10M polygons), this could take a long time, and all that waiting for Blender to respond makes this pipeline pretty inefficient.

### Cars

The flying cars are a relatively simple Niagara Visual Effect system. I modeled a low-poly car in Blender for the particles. Each system represented a single "lane" of cars and would randomly get a maximum speed assigned to it, from which all the spawning cars in that lane would get a random speed between 0.8x and 1x the maximum speed. Using the particle age and initial speed, I could calculate how far each car had flown and kill it after a fixed distance, and I placed 36 car lanes in the city each from one side to the other. For the car material, I used UE4's clear coat shader to look like metallic car paint and randomly chose between a yellow, blue, grey, or black base color per particle.

### Performance

This scene is big. There are 8 x 8 = 64 "megablocks" that all get a variety of large, medium, and small models iteratively scattered across them as well as several side walls, a door, and a high plane that receive similar scattering. In total, the scene had around 200 million triangles which my graphics card didn't enjoy. Fortunately, though, since I had divided the city meshes by object type and material, I was able to temporarily hide things like the dense side walls or flat panels that didn't affect camera blocking, allowing me to iterate on shading, lighting, and camerawork at 60 FPS.

### Unreal Engine

UE4 was a breeze to set up volumetric lighting, cinematics, and fast rendering. This was the first time I've really made a cinematic in Unreal, but it was really cool to see how effective Sequencer was for making all my shots and how nice the renders with 64 temporal subsamples per frame looked out of the Movie Render Queue.


## Disclaimer

This is a non-profit unofficial fan-artwork that is not intended for commercial use. It was made solely for the joy of learning and fun. Properties are owned by Lucasfilm/Disney and this fan-work is not connected in any way to said companies.

But please hire me, Lucasfilm!