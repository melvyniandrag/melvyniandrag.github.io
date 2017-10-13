TinkerCAD
################

:date: 2017-10-12
:modified: 2017-10-12 22:20
:tags: diy cad fun
:category: cad
:slug: tinkercad 
:authors: Melvyn Drag
:summary: About tinkercad.

**********************************
TinkerCAD!
**********************************

3D printing has been all the rage for years now. There is so much tech out there, it's tough to keep up with. Until the last couple of years I thought 3d printing would just be for making crude plastic shapes as a gimmick. It has turned out to be a rich industry / technology with lots of options for printers and CAD softwares, and the quality of the output is very good. 
TinkerCAD is a free, super user friendly CAD software that you can use to design an object for a 3d printer! It comes with a super easy to use `web interface <tinkercad.com/>`_. It also has the really cool ability to be scripted in javascript, so you can design really funky shapes using code! I guess you could design anything with the scripting api, but there are probably structural constraints. Since the printer is spitting out molten plastic, you probably can't get tooooo crazy with what you script, because it may not be stable. But then again, who knows? I've never printed one of the things I've designed. I'm sure the stability of the output is a function of the material, print speed, and print temperature. And other factors. 

Here's a cool JS script that prints a cube. Saw it on `youtube <https://www.youtube.com/watch?v=yLDwIQ0MPvA/>`_.

.. code-block:: javascript
    
    // Convenience Declarations For Dependencies.
    // 'Core' Is Configured In Libraries Section.
    // Some of these may not be used by this example.
    var Conversions = Core.Conversions;
    var Debug = Core.Debug;
    var Path2D = Core.Path2D;
    var Point2D = Core.Point2D;
    var Point3D = Core.Point3D;
    var Matrix2D = Core.Matrix2D;
    var Matrix3D = Core.Matrix3D;
    var Mesh3D = Core.Mesh3D;
    var Plugin = Core.Plugin;
    var Tess = Core.Tess;
    var Sketch2D = Core.Sketch2D;
    var Solid = Core.Solid;
    var Vector2D = Core.Vector2D;
    var Vector3D = Core.Vector3D;
    
    
    // Template Code:
    /*
      Empty shape example.
    
      Tinkercad developer documentation is at:
         https://tinkercad.com/developer/
    
      To create parameters in the user interface, 
      create a 'params' array on the top level of this script.
      For example:
      params = [
      { "id": "radius", "displayName": "Radius", "type": "length", "rangeMin": 1, "rangeMax": 50, "default": 20 }
      ]
    */
    
    function process(params) { 
      var mesh = new Mesh3D();
    
      mesh.quad([0, 0, 0],  [0, 10, 0],  [15, 10, 0],  [15, 0, 0] );
      mesh.quad([0, 0, 0],  [15, 0, 0],  [15, 0, 10],  [0,  0, 10] );
      mesh.quad([15, 0, 0], [15, 10, 0], [15, 10, 10], [15, 0, 10] );
      mesh.quad([0, 0, 0], [0, 0, 10], [0, 10, 10], [0, 10, 0] );
      mesh.quad([0, 10, 0], [0, 10, 10], [15, 10, 10], [15, 10, 0] );
      mesh.quad([0, 0, 10], [15, 0, 10], [15, 10, 10], [0, 10, 10] );
       
      
      return Solid.make(mesh);
    }
