# FAQs

**How do I apply grass to an object?**

- Simply drag and drop the material to the Material slot of a game object with a Mesh Filter set to the mesh that you want to render with the shader. You may want to duplicate the mesh to retain the shading used by the base mesh.

**Which types of objects can have grass?**

- Any non-skinned mesh can use the shader to render grass.

************************************Can I use this shader on a terrain?************************************

- No. This shader only works on non-terrain meshes. Support for terrain shading may be added in the future.

**************************************************************************************Does the asset work with skinned mesh renderers?**************************************************************************************

- No.

**Is the asset compatible with the Deferred Rendering Path?**

- The shader will render as part of the UniversalForwardOnly pass during the Deferred Rendering passes.

**********************************Does this asset write to the Depth Texture?**********************************

- Yes.

**************************************************************************************Does this asset write to the Depth Normals texture?**************************************************************************************

- Yes.

****************************************************************************************************Can I selectively render grass in some areas only?****************************************************************************************************

- Yes! You can exclude grass from rendering using two techniques: **Vertex Grass Coverage** and **Grass Coverage Mapping**.
- With the **Grass Coverage Map**, you can selectively render grass on certain parts of the mesh according to the UV coordinates from an input texture.
- With the **Vertex Grass Coverage** option, you can use the **Vertex Color** of your mesh to selectively render grass. The grass will render in areas of your mesh where the Vertex Color has a red channel value of 1. You can use [Polybrush](https://unity.com/features/polybrush) to paint your mesh vertices, or you can do it offline in your modeling engine.
    
    ![An example of Grass Coverage Mapping using a texture. This texture has a hard cutoff because it is text.](FAQs%20db750a71de5a4745bbd294112b40b6be/Untitled.png)
    
    An example of Grass Coverage Mapping using a texture. This texture has a hard cutoff because it is text.
    
    ![Another example of Grass Coverage Mapping. This texture has a gradual cutoff because it is a noise texture.](FAQs%20db750a71de5a4745bbd294112b40b6be/Untitled%201.png)
    
    Another example of Grass Coverage Mapping. This texture has a gradual cutoff because it is a noise texture.
    
    ![An example of Vertex Grass Coverage. You can use a tool like Polybrush to paint the Vertex Colors.](FAQs%20db750a71de5a4745bbd294112b40b6be/Untitled%202.png)
    
    An example of Vertex Grass Coverage. You can use a tool like Polybrush to paint the Vertex Colors.
    

**How are the textures sampled?**

- We offer two texture sampling methods. One is using mesh UVs. This is best for meshes where you want to draw grass even on steep angles (in world space). The other method is using the world space position on the XZ plane. This is typically a better choice for most meshes.
- All Textures will follow the active texture sampling method. The shader does not support split sampling (e.g., sample color in UV space and coverage in World Space).

![Example of a spherical mesh rendering using the world space method](FAQs%20db750a71de5a4745bbd294112b40b6be/Untitled%203.png)

Example of a spherical mesh rendering using the world space method

![The same mesh using the UV method](FAQs%20db750a71de5a4745bbd294112b40b6be/Untitled%204.png)

The same mesh using the UV method

**The grass sticks out past my mesh.**

- This can occur when you have `Fade Grass on Steep Surfaces` enabled. You can adjust the strength of this effect using the `Surface Normal Fading Power` setting. You can disable this feature and manually blend a secondary custom mesh into the source mesh. Alternatively, embrace your new easy grassy hat renderer.

![Example of a mesh with grass appearing to render past the base mesh when using Fade Grass on Steep Surfaces.](FAQs%20db750a71de5a4745bbd294112b40b6be/Untitled%205.png)

Example of a mesh with grass appearing to render past the base mesh when using Fade Grass on Steep Surfaces.

**What are these textures?**

- The grass textures are made up of three major sections: The grass colors, the grass density map, and the wind data.
- The grass colors use a ground texture (used at the bottom of the grass stack) and a grass texture (used at the top of the grass stack). We interpolate between these textures by the grass height. These are color (RGB) textures.
- The grass density map consists of a shape texture and a detail texture. We erode the grass shape through the cloud volume using these textures. These are single-channel (R) textures.
- The wind map is a single RGB texture. We remap this from [0, 1] to [-1, 1], and then use the RGB components as XYZ vectors to displace the grass UVs.

******************************I can see a cross-hatching pattern, especially on round or spherical objects. Can I remove that?******************************

- This hatching pattern can be caused by the fins. You can disable structured fins and customize the fins to an appropriate width, or you can disable fins entirely.

![Untitled](FAQs%20db750a71de5a4745bbd294112b40b6be/Untitled%206.png)

![Untitled](FAQs%20db750a71de5a4745bbd294112b40b6be/Untitled%207.png)

**I want some parts of my mesh not to render grass. Is that possible?**

- Yes. the grass only renders on mesh faces. The maximum height of the grass is attenuated according to the `Vertex Color R channel`. Using these two pieces of information, you have two options. You can hide grass by removing faces or by using the Vertex Color to attenuate the grass.
    - To hide grass by removing faces: duplicate your mesh, remove any faces that shouldn’t render the grass, then place the grass material on your new mesh.
    - To hide grass using the vertex color: paint the `Vertex Color R Channel` a value of 0 using a modeling tool like **Blender** or **Polybrush**. You can disable the Vertex Color blending basis if you use the Vertex Color for something else in your project. Painting the `Vertex Color R Channel` between 0 and 1 will result in grass that is shortened by the same ratio. (e.g., a value of 0.5 corresponds to grass that is 50% of the length described as the maximum length in the grass material being applied to the vertex).

**************************************************************How do I use the Wind settings?**************************************************************

- The wind consists of three parts: The main wind, wind pulses, and wind turbulence.
- The main wind is a static offset that tilts your grass in the `wind direction` according to the `main wind amplitude`.
- The wind pulses are a variable offset using sin waves that periodically increase the tilt in the `wind direction` according to the `wind pulse frequency` and `wind pulse magnitude`.

**************************************************What are shells and fins?**************************************************

- We render the grass as a series of extruded shells from the source mesh geometry. These are like thin layers, or `shells`, wrapped over the source mesh. These look great from above but disappear from the side.
- To maintain the shape of the grass when viewed from the side, we also render a set of vertical `fins` that extend from the source mesh geometry to the grass height. These fins look OK from the side, but they aren’t intended to be used alone. They are just rendered to maintain the volume of the grass when viewed from the side.

**************************What does the Grass Direction Map do?**************************

- The grass direction map lets you offset the grass vectors. Without the direction map, every blade of grass would point straight up. With the direction map, you can mix up the direction of the grass blades in order to generate more natural-looking grass. See below for a comparison with and without the Grass Direction map.

![Grass with a Direction Map applied](FAQs%20db750a71de5a4745bbd294112b40b6be/Untitled%208.png)

Grass with a Direction Map applied

![Grass without a Direction Map applied](FAQs%20db750a71de5a4745bbd294112b40b6be/Untitled%209.png)

Grass without a Direction Map applied

**Does this render Unity’s built-in fog?**

- Yes. You can choose whether this shader uses Unity’s built-in fog using the `Receive Fog` option.

![Untitled](FAQs%20db750a71de5a4745bbd294112b40b6be/Untitled%2010.png)

**What lighting model do you use?**

- The ground and grass use a custom lighting model which incorporates some elements of Schlick and GGX lighting methods. We assume the grass has a constant roughness value of 1 across all fragments. However, we do render diffuse, specular, and subsurface lighting components. We also recalculate the normal vectors of the grass to ensure that the grass blades are lit in a way that they appear to be vertical grass blades rather than horizontal slices.

******************Can I change the lighting model?******************

- No. You can, however, disable some contributions to the lighting, such as ambient lighting, direct lighting, and fog.

**************************Can I disable shadows?**************************

- Yes.
- You can disable the **Cast Shadows** via the **Shadow Caster Pass** with the Cast Shadows inspector toggle. When disabled, the grass will not cast shadows onto itself or onto other objects.
- You can also disable **Received Shadows** with the Receive Shadows inspector toggle. When disabled, the grass will not receive shadows from itself or from other objects.

**************************************************Does this receive decals?**************************************************

- Yes. The grass shader can receive decals. Each layer of the grass, including the ground layer, will sample the decal.

![Example of Grass Shader rendering with a Decal applied.](FAQs%20db750a71de5a4745bbd294112b40b6be/Untitled%2011.png)

Example of Grass Shader rendering with a Decal applied.

********************Which Shader Passes does this render into?********************

- “UniversalForwardOnly”. Used to render opaque geometry for both the Forward and Deferred rendering paths.
- “ShadowCaster”. Used to create the Shadowmap. Can be disabled via a toggle.
- “DepthOnly”. Used to create the Depth Texture. Can be disabled by editing the shader.
- “DepthNormals”. Used to create the DepthNormals texture. Can be disabled by editing the shader.

****************************************Does this render Ambient Lighting?****************************************

- Yes. You can also toggle whether this shader uses the Ambient Lighting using the `Receive Ambient Lighting` option.

![Untitled](FAQs%20db750a71de5a4745bbd294112b40b6be/Untitled%2012.png)

******************The grass has a visible cutoff at a certain radius from the camera. What is this and how do I fix it?******************

- This cutoff is caused by the shadow cascades. You can disable shadow casting, disable receiving shadows or adjust your shadow cascades.

![Untitled](FAQs%20db750a71de5a4745bbd294112b40b6be/Untitled%2013.png)

**What global performance controls do you offer?**

- Number of Grass Layers (maximum 20, minimum 4)
- Receive Shadows
- Cast Shadows
- Interactivity Enabled / Disabled
- Enable/Disable Fin Rendering
- Enable/Disable Shell Rendering

************What parameters do you offer?************

- Method
    - World Space
    - UV Space
- Colors
    - Grass Color (Texture + Color)
    - Ground Color (Texture + Color)
- Grass Mapping
    - Grass Exclusion Map
    - Surface Normal Exclusion Mapping
- Grass Density Mapping
    - Grass Density Map (Shape + Detail)
    - Grass Density Map Frequency (Shape + Detail)
    - Grass Density Map Strength (Shape + Detail)
- Lighting
    - Receive Direct Lighting Enabled
    - Receive Ambient Lighting Enabled
    - Receive Shadows Enabled
    - Subsurface Scattering Enabled
        - Subsurface Scattering Power
    - Ambient Occlusion Enabled
        - Ambient Occlusion Strength
    - Receive Fog Enabled
- Wind
    - Receive Global Wind Enabled
    - Wind Strength
    - Wind Frequency
    - Wind Direction
    - Wind Color
    - Wind Color Strength
        - Wind Color (As `abs(Wind Value)` increases, `Grass Color` has `Wind Color` added to it)

**************************What do the Fade Start Distance and Maximum Distance settings do?**************************

- The Maximum Distance is the maximum distance at which grass shells will render.
- The Fade Start Distance is the distance at which the grass shells begin to fade out. This is also the maximum distance at which grass fins will render.
- The Maximum Distance helps you to improve performance by reducing the amount of new geometry (as Shells) that must be created in each frame for the grass effect.
- The Fade Start Distance helps you to improve performance by reducing the amount of new geometry (as Fins) that must be created in each frame for the grass effect.
- The Fade Start Distance also helps you smoothly blend out the grass effect.

![Example of a Grass Fade Start Distance of 20 and Maximum Distance of 23](FAQs%20db750a71de5a4745bbd294112b40b6be/Untitled%2014.png)

Example of a Grass Fade Start Distance of 20 and Maximum Distance of 23

**********************************************************************************What interactivity options are available?**********************************************************************************

- **REMOVE** the grass. Indicated by a **Red** color. The grass will be gradually removed as the Red color channel value increases from 0 to 1. When the Red color channel is 1, only the ground will show.
- **PRESS** the grass. Indicated by a **Green** color. The grass will be proportionally cut by the `_GrassInteractivityCutAmount` as the Green color channel value increases from 0 to 1.
- **TINT** the grass. Indicated by a **Blue** color. The grass will be proportionally tinted by the `_GrassInteractivityTint` as the Blue color channel value increases from 0 to 1. This is a multiplicative operation to the grass albedo. This also tints the ground color.

![Example using the Remove system. The particle color is set to red, so the particles will completely remove the grass where it appears. ](FAQs%20db750a71de5a4745bbd294112b40b6be/Untitled%2015.png)

Example using the Remove system. The particle color is set to red, so the particles will completely remove the grass where it appears. 

![Example using the Press system. The particle color is set to green, and the _GrassInteractivityCutAmount is set to 0.7. A value of 0.7 indicates that we want the final grass height to be 70% of the original grass height.](FAQs%20db750a71de5a4745bbd294112b40b6be/Untitled%2016.png)

Example using the Press system. The particle color is set to green, and the _GrassInteractivityCutAmount is set to 0.7. A value of 0.7 indicates that we want the final grass height to be 70% of the original grass height.

![Example using the Tint system. The particle color is set to blue, but the _GrassInteractivityTint color is set to Red.](FAQs%20db750a71de5a4745bbd294112b40b6be/Untitled%2017.png)

Example using the Tint system. The particle color is set to blue, but the _GrassInteractivityTint color is set to Red.

********************************************************How does interactivity work?********************************************************

- Place an orthographic camera in your scene.
- You can remove the Audio Listener component from the camera.
- Set the camera’s Background Type to Solid Color, and the Background to **Black**.
- You can adjust the **Size** and **Clipping Planes**.
- Set the layer masks on the camera to `TransparentFX`, or to the layer mask you’d like to render.
- **Reminder:** Make sure that you remove this Layer Mask from the Culling Mask used by your normal rendering camera 🙂
- Add the `OS_GrassInteractivityRenderer.cs` script to this camera.
- Assign a target transform to the script if you want the camera to move automatically during runtime or move it yourself via a custom script. No special handling is needed here.
- Add one of the Interactive Particle System prefabs to the object you’d like to interact with the grass.
- You can have any number of interactive particle systems in your scene, but this asset is designed to use **only one orthographic camera** for interactivity. If more than one interactivity renderer is present in a scene, the behavior is **undefined**.

**************The interactive section of the grass is a little jittery when I move the camera.**************

- This can happen when your camera size is too large, or your render texture resolution is too small. This is caused by aliasing in the render texture. You can reduce this effect by reducing your camera size, increasing your render texture resolution, or both.

**********************************************************************************************How do I increase my render texture resolution?**********************************************************************************************

- Open the ******************************Output Texture****************************** on the Orthographic Camera. Change the **Size** property. Note that the Size property’s x and y components should always be the same. Keep in mind the Camera Size property when adjusting your Render Texture Resolution.

**The interactivity texture fades out near the edges.**

- That’s right. We automatically fade out the interactivity texture for the last 20% of the radius of the interactivity camera. This helps the interactivity texture to fade smoothly in and out instead of rendering a hard edge at the boundaries of the texture.

![Untitled](FAQs%20db750a71de5a4745bbd294112b40b6be/Untitled%2018.png)

**What types of Particle Systems, Objects, or Materials can I use for the Interactive Texture?**

- You can use any object, particle system, VFX Graph, or material - as long as it is set to the same layer mask that will be rendered by the Grass Interactivity Camera.

![Example of rendering a Particle System into the Interactivity Texture.](FAQs%20db750a71de5a4745bbd294112b40b6be/Untitled%2019.png)

Example of rendering a Particle System into the Interactivity Texture.

![Example of rendering a mesh into the Interactivity Texture.](FAQs%20db750a71de5a4745bbd294112b40b6be/Untitled%2020.png)

Example of rendering a mesh into the Interactivity Texture.

**Do Additional Lights affect the Grass?**

- Yes, the grass can receive Additional Lighting. For performance reasons, Grass will not receive cast shadows from Additional Lights.

**I don’t see shadows when only Fins are enabled.**

- We have disabled Fin Shadow Rendering because they cause artifacts at steep sun angles.

****************************************************Can the grass receive fog?****************************************************

- Yes, toggle the Receive Fog option.

**********************How does the grass respond to sharp viewing angle?**********************

- We generate an extra set of triangles stood up vertically as billboards to ensure the grass looks good from any angle

**********************************************************************************************************What color format should I use for my Grass Interactivity Render Texture?**********************************************************************************************************

- I recommend using the R8G8B8A8_UNORM color format. The grass interactivity system does not require a great degree of color precision to work well, so this 8-bit color format is sufficient. If you only plan to use the R or RG color channels, you can switch to an R8_UNORM or R8G8_UNORM color format.

******************************************************************************************I’m getting an error about Render Textures when Decals are enabled******************************************************************************************

- You may get this error in your console when Grass Interactivity and Decals are both enabled: `RenderTexture.Create failed: colorFormat & depthStencilFormat cannot both be none.`
- To fix this issue, you can use one of two approaches:
- **Approach 1:** Ensure your Grass Interactivity Target Texture has both a Color Format **and** a Depth Stencil Format. I set my Depth Stencil Format to D16_UNORM.
- **Approach 2:** You can assign a separate renderer to the Grass Interactivity camera. This separate renderer should not include the Decals Render Feature.

******************************************************Does this asset work on WebGL?******************************************************

- No. WebGL doesn’t support Geometry shaders, so this asset does not work on WebGL. This asset will work on any platform with Geometry shader support. Note that it has only been tested on Windows PC at this time.

************************I need help.************************

- Contact us at [hello@occasoftware.com](https://www.notion.so/OccaSoftware-3f5420be1ef8480d9ddd87c553e72248) or send a message on our Discord, [http://occasoftware.com/discord](http://occasoftware.com/discord)

**I want a refund.**

- Email us at [hello@occasoftware.com](https://www.notion.so/OccaSoftware-3f5420be1ef8480d9ddd87c553e72248) with your Invoice #. We’ll refund you, no questions asked.
