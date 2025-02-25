Aftershock: A 3D rendering engine
----------------------------------

Aftershock is a Quake3Arena-like rendering engine.  At the moment, the
focus is on the rendering and not gameplay, networking, etc.  It was
started as an exercise in learning advanced OpenGL and game writing
techniques, and I hope it will prove useful to others with the same
goal.

Currently Aftershock supports the following:

o Reading BSP, MD3, textures, etc. from Q3A pk3 files.
o BSP tree culling.
o PVS set testing/culling.
o Face sorting.
o Lightmaps.
o Shader script parsing - most essential shader commands supported, 
  others to follow. (almost all commands should be supported now (?) )
o Animations.
o Curved surface (bezier patch) generation.
o Skybox drawing.
o Trisurf and MD3 model drawing.  // multiframe supported now 

- Collision Detection (not fully Q3 compatible but it should work )  



The following things aren't yet implemented:

o Fog volumes.
o Environment mapping. // a hack version is supported , not cube mapping 
o Portals/mirrors.  // help !!!
o Flares (anybody know what these are?)  // do not implement (Q3 does not ,too )
o Dynamic lighting (there is nothing dynamic yet, so there is no forum 
  for this)

Other areas for improvement are:

o Code structure.  This was written as a learning exercise over a
  couple of months, so it was evolved rather than designed.
o Optimization.  It doesn't have the Q3A speed yet.  The biggest area
  here is the rendering backend.  Multitexturing and compiled vertex
  arrays would help a lot, but they aren't supported by the GL driver
  on my development platform (Linux/TNT2) yet :-(
- Cleanups 
- Porting to other Platforms ( only WIN32 supported at the moment )
- Getting the Q3 network protocol running ! ( then it would be no  bigger problem (exception : time !) )
 


Aftershock works with the 4 levels that come with the Q3A demo.  I
don't have the full game (yet), so I don't know about those levels.
But I'm guessing most will work to some degree.  Things like
underwater areas may fail completely.  

Earlier Q3A test files will not work.

INSTALLATION
------------

Aftershock has been tested on i386-linux-gcc and Windows98-MSVC.  
It won't work as-is on big-endian systems.  The following libraries  // should now work on BE
are required:

- libjpeg (http://www.ijg.org/)
- zlib (http://www.cdrom.com/pub/infozip/zlib/)
- opengl, glu
- glut (optional on linux, see Makefile)

Building for Linux is straightforward ('make').  For windows do:

nmake -f Makefile.win

STRUCTURE
---------

The bsp.h file should give a good idea of the structure of a bsp
file.  They are not terribly different from previous bsp formats
(e.g. Quake, Quake2).  One major difference is that textures are
stored externally.  Furthermore, instead of each face (surface)
pointing to a texture, they point to shaders.  A shader is a
procedural texturing script with potentially multiple passes, stored
in text files.  Often a referenced shader is missing from the shader
script files.  In this case a default shader is used.

Aftershock's approach to shaders is:
1. Load the BSP file.
2. Load the MD3 models referenced in the BSP entities list.
3. Add any shaders (strings) referenced in the MD3 files to the BSP
   shader references list.
4. Parse the shader scripts, looking for matches with the shader
   references list.
5. Create default shaders for missing references.
6. Load all textures required by the referenced shaders.

This seems to differ from Q3A's behavior, which loads the MD3 models
last...

The rendering 'pipeline' is divided into a frontend and backend.  The
frontend traverses the BSP tree, performing as much object level
culling as possible, and generates a list of things to draw.  The list 
is sorted to minimize texturemap and render state switches during
drawing.  The backend draws the list by first pushing all triangles
with the same render state/texturemap into an array, setting the
render state and calling glDrawElements() to draw the array.
Triangles are in tristrip order, but are discrete.  See John Carmak's
description of this at:
   http://www.gamers.org/dEngine/quake3/johnc_glopt.html

DEVELOPMENT
-----------

I encourage tinkering with this code to improve things and add new
features.  Please submit patches to me so I can pass them along.  If I
get enough interest, I'll open the CVS, create a discussion list, etc.

I also appreciate any comments.

Steve Taylor
<lazyX@home.com>

Additions and changes made by 
Martin Kraus <m_kraus@excite.de>
