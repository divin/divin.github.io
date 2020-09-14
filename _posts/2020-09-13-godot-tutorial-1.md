---
title: "Tutorial 1: Drawable & Draggable Paper In Godot"
toc: true
toc_sticky: true
toc_label: "Content"
categories:
  - Tutorials
tags:
  - Godot
  - Tutorial
  - Taikun
excerpt: "We make a drawable and draggable paper in Godot."
---

# Introduction

In this tutorial I'll show you how to make a drawable and draggable paper in Godot. The result will look like this:

![Result](/assets/images/posts/tutorial_1/result.gif "Result")

This tutorial is more aimed for people with a bit of knowledge in Godot and GDScript.
I like to use a bit more Object Oriented Programming (OOP) style and usually try to define the variables and methods correctly by limiting
the data type (for example `var foo : String = "bar"`). This is helpful to prevent misuse of variables/methods and make bug fixing a bit easier in my opinion.  

Download links for the source code are on my GitHub (or visit the [Download section](#download)) :)

# Project settings

The only changes I did to the project settings were made under `Display/Window`:

```
Width = 320
Height = 180
...
Test Width = 1280
Test Height = 720
...
Mode = 2d
Aspect = expand
```

# Creating the paper scene

We will start with the interesting part, the paper itself. We make a new scene, use a `TextureRect` as our root node and name it "Paper". I didn't changed any settings in the inspector.
Depending on your aim you want to add now a texture to the `TextureRect` or later in the `Main` scene.

## Set variables 

Next, we attach a script to it. I usually like to give the script a `class_name`, the name of the script itself (in our case `Paper`). Let's define some variables:

```gdscript
class_name Paper
extends TextureRect

# Bool to check if pen is selected
var pen_selected : bool

# Drawing color of the pen
const color : Color = Color( 0, 0, 0, 1 ) # Black

# Saves the Position if the Paper is being dragged
var drag_position : Vector2 = Vector2.ZERO

# Save the previouse Mouse position
var previous_mouse_position : Vector2 = Vector2.ZERO

# Create an Node2D for the pen when ready
onready var pen : Node2D = Node2D.new()
```

Most of them are self-explanatory (or explained by the comments), the `pen` variable, which is an `Node2D`, is used to create our "pen". We will call the `draw` method from this Node to do the drawing.

## Setter function

We also need a function to set our variable `pen_selected` from outside the script:

```gdscript
# Setter function for pen_selected
func set_pen_selected(is_selected : bool) -> void:
  self.pen_selected = is_selected
```

## Make drawing possible

Now is time to make the drawing possible

```gdscript
func make_drawing_possible() -> void:

  # Create Viewport to render the drawing
  var viewport = Viewport.new()
  var rect = self.get_rect() # Get the size of the TextureRect
  viewport.size = rect.size # Set size of viewport to the size of TextureRect
  viewport.usage = Viewport.USAGE_2D # Render Mode
  viewport.render_target_clear_mode = Viewport.CLEAR_MODE_ONLY_NEXT_FRAME # Works better than CLEAR_MODE_NEVER
  viewport.render_target_v_flip = true # OpenGL flips render target so we have to flip it again
  viewport.transparent_bg = true # Set Background transparent so we see the drawing
  viewport.add_child(self.pen) # Add the pen as child to the Viewport

  self.pen.connect("draw", self, "_on_draw") # Connect _on_draw with the draw method from pen
  self.add_child(viewport) # Add viewport as child
  
  # Use a sprite to display the result texture
  var rt = viewport.get_texture() # Which is transparent
  var board = TextureRect.new() # Make a canvas (also TextureRect)
  board.set_texture(rt) # Set texture
  self.add_child(board) # Add canvas
```

I've commented the code as good as possible to get an understanding what is happening. Credit goes to a user called "Zylann" from this [post](https://godotengine.org/qa/24621/painting-game-persist-drawing). I had to change some bits to suit my needs. 

Basically we create an new `Viewport` to render the drawing (with the same size as our `Paper`) and add our `pen` as child. We connect the `_on_draw` method (which we will define in a bit) to the `draw` method of the `pen`. After that we add the `Viewport` as child to the `Paper`, create another `TextureRect` which suits us as canvas.


## Get ready and process

The next step is simple, we call our monstrosity of function `make_drawing_possible` in `_ready` and update the `pen` in `_process`.

```gdscript
func _ready() -> void:
  self.make_signature_possible()

func _process(_delta) -> void:
  self.pen.update()
```

## Draw

Now it's time to define `_on_draw`:

```gdscript
func _on_draw() -> void:
  if self.pen_selected:
    var mouse_pos = self.get_local_mouse_position() # Get local mouse position

    if Input.is_mouse_button_pressed(BUTTON_LEFT):
      self.pen.draw_line(mouse_pos, self.previous_mouse_position, self.color) # Finally draw something
    self.previous_mouse_position = mouse_pos # Update previous mouse position
```

If `pen_selected == true` we get the mouse position and if we also press the left mouse button we start drawing. Before we return we update the `previous_mouse_position`.

## Dragging

Now to make the paper also draggable we select `Paper` (the `TextureRect`) in the scene view and click at the `Node` tab (usually next to the `Inspector` tab). Here we want to connect the `gui_input(event : InputEvent)` with our script. A function should be automatically generated in the `Paper.gd` once connected. The name will be differ depending how you named the `TextureRect`.

```gdscript
func _on_Paper_gui_input(event : InputEvent) -> void:

  if not self.pen_selected:
    if event is InputEventMouseButton:
      if event.pressed:
        self.drag_position = self.get_global_mouse_position() - self.rect_global_position
      else:
        self.drag_position = Vector2.ZERO
    if event is InputEventMouseMotion and drag_position != Vector2.ZERO:
      self.rect_global_position = self.get_global_mouse_position() - self.drag_position
```

If we didn't select the pen and the `InputEvent` recognized an `event` of type `InputEventMouseButton` (we clicked a mouse button) and is `pressed` we want to update the `drag_position`. Otherwise we want to set the `drag_position` to a zero vector. If there is an `event` type `InputEventMouseMotion` (we moved the mouse) and the `drag_position` is not a zero vector we want to update the position of the `TextureRect`.

# Creating the main scene

Now we getting closer to the finish. We make now our `Main` scene. Simply by creating an new scene, choose a basic `Node2D` as root node and name it "Main". Before we start programming we drag and drop the `Paper` scene inside our `Main` scene.

## Set variables

Similar to `Paper` scene we create some variables. A variable `paper` which refers to the just added `Paper` scene and `pen_selected` which is the same as we used in `Paper.gd`.

```gdscript
class_name Main
extends Node2D

# Grab the Paper when ready
onready var paper : Paper = $Paper

# Bool to store if pen is selected
var pen_selected : bool = false
```

## Get input

We now want to be able to switch between dragging and drawing. This is done by a simple key press (in this case "A").

```gdscript
func _input(event) -> void:

  # Bool to check if the button is just pressed (not needed if you assigned a action key)
  var just_pressed : bool = event.is_pressed() and not event.is_echo()

  # Check if the "A" button was just pressed
  if Input.is_key_pressed(KEY_A) and just_pressed:
    self.select_pen()
```

If the "A" key is just pressed, we want to call a method `select_pen` which communicates between the `Main.gd` and `Paper.gd`.

## Select the pen

The last step is to set `pen_selected` in `Paper.gd`:

```gdscript
func select_pen() -> void:
  # Function which selectes the pen

  # Negate pen_selected
  self.pen_selected = not self.pen_selected

  # Set pen_selected from the paper
  paper.set_pen_selected(self.pen_selected)
```

Now you should be able to draw on the paper :)

# Conclusion

If you followed this tutorial correctly, you should be able to drag a paper and also draw on it. Based on this you can expand this mechanic for all kinds of application. I hope you enjoyed it!

I'm making a little game based on this mechanic, I'll post other tutorials where we expand this idea a bit further. So stay tuned! :)

# Download <a name="download"></a>

You can find the the source code for this tutorial and all upcoming tutorials [here](https://github.com/divin/Godot-Tutorials).