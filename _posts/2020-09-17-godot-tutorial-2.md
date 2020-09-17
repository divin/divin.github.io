---
title: "Tutorial 2: Drawable & Draggable Paper In Godot (Improved)"
toc: true
toc_sticky: true
toc_label: "Content"
categories:
  - Tutorials
tags:
  - Godot
  - Tutorial
  - Taikun
excerpt: "We improve our mechanic from the previous tutorial."
---

# Introduction

Today we are improving our mechanic from [Tutorial 1](https://divin.github.io/tutorials/godot-tutorial-1/). The result will look like this:

![Result](/assets/images/posts/tutorial_2/result.gif "Result"){: .align-center}

Looks like similar as before, right? Indeed but we got rid of the "A" button to switch between draw and drag mode. Also, we got some fancy mouse cursors which indicates when we are able to drag or draw. Thanks to the guys on the [Godot subreddit](https://www.reddit.com/r/godot/), who gave me feedback on my previous tutorial and [Kicked-in-Teeth](https://kicked-in-teeth.itch.io/grab-hand) for the Grab Hand package which I used for the mouse cursor. You can download it for free and use it to follow along the tutorial but be aware I flipped the images horizontally.

Again a little disclaimer:

*This tutorial is more aimed for people with a bit of knowledge in Godot, GDScript and programming in general.
I like to use a bit more Object Oriented Programming (OOP) style. Usually I try to define the variables and methods correctly by limiting
the data type (for example* `var foo : String = "bar"` *). This is helpful to prevent misuse of variables/methods and makes bug fixing a bit easier in my opinion.*

Download links for the source code are on my GitHub (or visit the [Download section](#download)) :)

# Project Settings

These are the same as for the [previous tutorial](https://divin.github.io/tutorials/godot-tutorial-1/#project-settings). Please have a look there!

# Creating the main scene

We start with something simple this time, the main scene. Just create a `Node2D`, name it "Main" and we are done. 
This time we don't need a script as everything will be handled by the `Paper` scene itself. Fancy, right? :) 

# Creating the paper scene

Now to the interesting part, we create a new scene, select as our root node a `TextureRect` and name it "Paper".

![TextureRect](/assets/images/posts/tutorial_2/texture_rect.gif){: .align-center}

After that you might want to set a texture for the `TextureRect`. This makes the next step a bit easier.

## Adding `Area2D` objects

`Area2D` objects are great to detect collision. In our case, we use it to have two different areas on our paper that we can distinguish. So, let's start.  

We add the first `Area2D` as a child to our `Paper` and name it "DragArea". To this area we add a `CollisionShape2D` as child this is our collision area. As shape we choose a Rectangle.
You should align the `CollisionShape2D` to fit the top part of the paper. It should look like this if you're done: 

![area_1](/assets/images/posts/tutorial_2/first_area.png){: .align-center}

For the second `Area2D` we do basically the same thing except we name it "DrawArea" and align the `CollisionShape2D` to fit the lower part of the paper. 
When done our paper should look like this:

![area_2](/assets/images/posts/tutorial_2/second_area.png){: .align-center}

For convenience I left a gap between the two areas. You should avoid overlapping areas.

![paper](/assets/images/posts/tutorial_2/paper_scene.png){: .align-left}
Our scene should look like the picture on the left. We are done for the moment, so let's add a script to our `Paper` and call it "Paper".

## Set variables

As usual we begin with our script by defining some variables which we need later. The `class_name` will be "Paper".
In essence we need the following: 

* a color for our pen, saved in the constant `color_black`,
* some boolean data types which we use to track if we are at the moment `dragging` or `drawining`
* `Vector2` to track the `drag_position` and the `previous_mouse_position`
* an `Node2D` which will be used as our pen
* the name of our areas as strings (`drag_area_name` and `draw_area_name`)
* and last but not least some fancy mouse cursors (thanks again to [Kicked-in-Teeth](https://kicked-in-teeth.itch.io/grab-hand))

```gdscript
class_name Paper
extends TextureRect

# Color
const color_black : Color = Color( 0, 0, 0, 1 ) # Black

# Bools
var dragging : bool = false # dragging mode
var drawing : bool = false # signing mode

# Vector2
var drag_position : Vector2 = Vector2.ZERO # drag vector
var previous_mouse_position : Vector2 = Vector2.ZERO # previouse mouse position vector

onready var pen : Node2D = Node2D.new() # Create an Node2D for the pen when ready

# Name of Areas
var drag_area_name : String = "DragArea" # Name of the Drag Area
var draw_area_name : String = "DrawArea" # Name of the Draw Area

# Mouse Cursor
var standard_mouse = preload("res://assets/Sprites/Mouse_Cursor/hand_01.png") # Standard Coursor

var on_drag_mouse = preload("res://assets/Sprites/Mouse_Cursor/hand_02.png") # Cursor when over Drag Area
var drag_mouse = preload("res://assets/Sprites/Mouse_Cursor/hand_06.png") # Cursor when dragging

var on_draw_mouse = preload("res://assets/Sprites/Mouse_Cursor/hand_08.png") # Cursor when over Draw Area
var draw_mouse = preload("res://assets/Sprites/Mouse_Cursor/hand_03.png") # Cursor when drawing
```

## Ready and process

In the `_ready` method we only call our `make_drawing_possible` function which is not really different to the one in the previous tutorial. 
The `_process` method should update our `pen` and also our mouse cursor. We get following:

```gdscript
func _ready() -> void:
 self.make_drawing_possible()
	
func _process(_delta) -> void:
 self.pen.update()
 self.update_mouse()
```

## Make drawing possible

To make drawing possible we use the same two methods (`make_drawing_possible` and `_on_draw`) as in my previous tutorial. For more details see [here](https://divin.github.io/tutorials/godot-tutorial-1/#make-drawing-possible) and [here](https://divin.github.io/tutorials/godot-tutorial-1/#draw).


```gdscript
func make_drawing_possible() -> void:
 # Create Viewport to render the drawing
 var viewport : Viewport = Viewport.new()
 var rect : Rect2 = self.get_rect()
 viewport.size = rect.size
 viewport.usage = Viewport.USAGE_2D # Render Mode
 #viewport.render_target_clear_mode = Viewport.CLEAR_MODE_ONLY_NEXT_FRAME # Never Clear
 viewport.render_target_clear_mode = Viewport.CLEAR_MODE_ONLY_NEXT_FRAME # Works better!
 viewport.render_target_v_flip = true # OpenGL flips render target we have to flip it again
 viewport.transparent_bg = true # Set Background transparent so we see the drawing
 viewport.add_child(self.pen) # Add the pen as child to viewport
 var error = self.pen.connect("draw", self, "_on_draw") # Connect _on_draw with the draw method from pen
 if error != OK: # If we get an error
  printerr("Couldn't connect Pen. Error Code: ", error)
 self.add_child(viewport) # Add viewport as child

 # Use a sprite to display the result texture
 var rt : Texture = viewport.get_texture()
 var board : TextureRect = TextureRect.new()
 board.set_texture(rt)
 self.add_child(board)

# Draw Method
func _on_draw() -> void:
 var mouse_pos : Vector2 = get_local_mouse_position()
 if self.drawing:
  if Input.is_mouse_button_pressed(BUTTON_LEFT):
   self.pen.draw_line(mouse_pos, self.previous_mouse_position, self.color_black)
 self.previous_mouse_position = mouse_pos # Update previouse position
```

## Change mouse cursor

To change the mouse cursors we need the method `update_mouse`. This method basically just checks if we are hovering over the specific area (`DragArea` or `DrawArea`) or if we are `dragging` or `drawing` by calling the mysterious method `is_point_in_area`, which we will discuss in a bit.

```gdscript
func update_mouse():
 var mouse_pos : Vector2 = self.get_global_mouse_position() # Get mosue position
 var on_drag : bool = self.is_point_in_area(mouse_pos, drag_area_name) # Check if mouse is in drag area
 var on_draw : bool = self.is_point_in_area(mouse_pos, draw_area_name) # Check if mouse is in draw area
 if on_drag and not Input.is_mouse_button_pressed(BUTTON_LEFT): # If we hover over drag area
  Input.set_custom_mouse_cursor(on_drag_mouse) # Set mouse cursor
 elif self.dragging:
  Input.set_custom_mouse_cursor(drag_mouse) # Set mouse cursor
 elif on_draw and not Input.is_mouse_button_pressed(BUTTON_LEFT): # If we hover over draw area
  Input.set_custom_mouse_cursor(on_draw_mouse) # Set mouse cursor
 elif self.drawing:
  Input.set_custom_mouse_cursor(draw_mouse)
 elif not on_drag and not on_draw: # If not over these areas
  Input.set_custom_mouse_cursor(standard_mouse)
```

## Are we in the area?

One of the important methods in this script is `is_point_in_area`. The issue I encounter is that if you are using `TextureRect` and a `Signal` from it, like `gui_input` (which we will use in the next section), every `InputEvent` will be blocked and not passed through to the child objects (at least in Godot 3.2.2). This means, we can't just use the `Signal` `mouse_entered` from the `Area2D` object to detect if we are inside the area because all input signals will be blocked by the `TextureRect` object. The mouse filter option on the `TextureRect` is unfortunately not helpful as we also want to drag the paper.

To solve this issue I ended up checking if a point is inside an area by using the `intersect_point` method. See the [Docs](https://docs.godotengine.org/en/stable/classes/class_physics2ddirectspacestate.html#method-descriptions) for some more information about this. The important bit was to set `collide_with_areas = true`.
The method will return an `Array` with all colliders we are intersecting. We just loop through these and check if one of the names in equal to the area we are looking for.

```gdscript
# Function which checks if a point (Vector2) is inside an Area2D
func is_point_in_area(point : Vector2, area_name : String) -> bool:
 var output : bool = false # Our output, default false
 var space_state : Physics2DDirectSpaceState = get_world_2d().direct_space_state # Get space state of all items
 var result : Array = space_state.intersect_point(point, 32, [], 2147483647, true, true) # Check if points intersect with something
 if result != []: # If we insect with something
  for item in result: # Go through items in the array
   if item["collider"].name == area_name: # Check if collider name equals the name we are looking for
    output = true # set our output to true
 return output
```

## Dragging

To make dragging possible we use the same method as in our previous tutorial. For more details see [here](https://divin.github.io/tutorials/godot-tutorial-1/#dragging).
I have rewritten the code a bit to make it clearer but the idea is the same. The only big difference is that we are just getting the position of the mouse once clicked and give this point to two methods `drag_paper` and `draw_paper`.

```gdscript
func _on_Paper_gui_input(event):
 # Check if we pressed a mouse button
 if event is InputEventMouseButton:
  # Check if we pressed left mouse button
  if event.button_index == BUTTON_LEFT and event.pressed:
   var point : Vector2 = self.get_global_mouse_position() # Get global mouse position
   # Call methods
   self.drag_paper(point)
   self.draw_paper(point)
  else:
   # Else set dragging and drawing false
   self.dragging = false
   self.drawing = false
 # Check if we moved the mouse while we drag the paper
 if event is InputEventMouseMotion and drag_position != Vector2.ZERO and self.dragging:
  # Set new position
  self.rect_global_position = self.get_global_mouse_position() - self.drag_position
```

## Drag & Draw

In essence these methods only check if we are inside the areas and set the boolean data types `dragging` and `drawing`. The method `drag_paper` does also some fancy stuff by updating the `drag_position`.

```gdscript
# Drag Method which checks if we are dragging the paper
func drag_paper(point : Vector2) -> void:
 self.dragging = is_point_in_area(point, drag_area_name) # Check if mouse is in drag area
 if self.dragging:
  self.drag_position = self.get_global_mouse_position() - self.rect_global_position # Update drag position
 else:
  self.drag_position = Vector2.ZERO # Set drag position to zero vector

# Draw Method which checks if we are drawing on the paper 
func draw_paper(point : Vector2) -> void:
 self.drawing = is_point_in_area(point, draw_area_name) # Check if mouse is in draw area
```

# Conclusion

That's it! If you followed this tutorial correctly, you should be able to drag a paper and also draw on it. Based on this you can expand this mechanic for all kinds of application. I hope you enjoyed it!

I'm making a little game based on this mechanic, I'll post other tutorials where we expand this idea a bit further. So stay tuned! :)

# Download <a name="download"></a>

You can find the the source code for this tutorial and all previous and upcoming tutorials [here](https://github.com/divin/Godot-Tutorials).