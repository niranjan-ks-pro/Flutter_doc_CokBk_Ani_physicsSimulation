#ani_physics_simulation

Animate a widget using a physics simulation
===========================================

1.  [Cookbook](https://docs.flutter.dev/cookbook)
2.  [Animation](https://docs.flutter.dev/cookbook/animation)
3.  [Animate a widget using a physics simulation](https://docs.flutter.dev/cookbook/animation/physics-simulation)

Physics simulations can make app interactions feel realistic and interactive. For example, you might want to animate a widget to act as if it were attached to a spring or falling with gravity.

This recipe demonstrates how to move a widget from a dragged point back to the center using a spring simulation.

This recipe uses these steps:

1.  Set up an animation controller
2.  Move the widget using gestures
3.  Animate the widget
4.  Calculate the velocity to simulate a springing motion

[](https://docs.flutter.dev/cookbook/animation/physics-simulation#step-1-set-up-an-animation-controller)Step 1: Set up an animation controller
----------------------------------------------------------------------------------------------------------------------------------------------

Start with a stateful widget called `DraggableCard`:

content_copy

```
import 'package:flutter/material.dart';

void main() {
  runApp(const MaterialApp(home: PhysicsCardDragDemo()));
}

class PhysicsCardDragDemo extends StatelessWidget {
  const PhysicsCardDragDemo({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(),
      body: const DraggableCard(
        child: FlutterLogo(
          size: 128,
        ),
      ),
    );
  }
}

class DraggableCard extends StatefulWidget {
  const DraggableCard({required this.child, super.key});

  final Widget child;

  @override
  State<DraggableCard> createState() => _DraggableCardState();
}

class _DraggableCardState extends State<DraggableCard> {
  @override
  void initState() {
    super.initState();
  }

  @override
  void dispose() {
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Align(
      child: Card(
        child: widget.child,
      ),
    );
  }
}
```

Make the `_DraggableCardState` class extend from [SingleTickerProviderStateMixin](https://api.flutter.dev/flutter/widgets/SingleTickerProviderStateMixin-mixin.html). Then construct an [AnimationController](https://api.flutter.dev/flutter/animation/AnimationController-class.html) in `initState` and set `vsync` to `this`.

info Note: Extending `SingleTickerProviderStateMixin` allows the state object to be a `TickerProvider` for the `AnimationController`. For more information, see the documentation for [TickerProvider](https://api.flutter.dev/flutter/scheduler/TickerProvider-class.html).

Run and verify the logo in app.

Below new lines to has to be added in ti your existing code like below. 

lib/{starter.dart → step1.dart} Viewed

|  |

@@ -29,14 +29,20 @@

 |
|

29

29

 |

   State<DraggableCard> createState() => _DraggableCardState();

 |
|

30

30

 |

  }

 |
|

31

 |

- class _DraggableCardState extends State<DraggableCard>~~  {~~

 |
|

31

 |

+ class _DraggableCardState extends State<DraggableCard>

 |
|

32

 |

+  with SingleTickerProviderStateMixin {

 |
|

33

 |

+  late AnimationController _controller;

 |
|

34

 |

+

 |
|

32

35

 |

   @override

 |
|

33

36

 |

   void initState() {

 |
|

34

37

 |

   super.initState();

 |
|

38

 |

+  _controller =

 |
|

39

 |

+  AnimationController(vsync: this, duration: const Duration(seconds: 1));

 |
|

35

40

 |

   }

 |
|

36

41

 |

   @override

 |
|

37

42

 |

   void dispose() {

 |
|

43

 |

+  _controller.dispose();

 |
|

38

44

 |

   super.dispose();

 |
|

39

45

 |

   }

 |

[](https://docs.flutter.dev/cookbook/animation/physics-simulation#step-2-move-the-widget-using-gestures)Step 2: Move the widget using gestures
----------------------------------------------------------------------------------------------------------------------------------------------

Make the widget move when it's dragged, and add an [Alignment](https://api.flutter.dev/flutter/painting/Alignment-class.html) field to the `_DraggableCardState` class:

lib/{step1.dart (alignment) → step2.dart (alignment)} Viewed

|  |

@@ -1,3 +1,4 @@

 |
|

1

1

 |

  class _DraggableCardState extends State<DraggableCard>

 |
|

2

2

 |

   with SingleTickerProviderStateMixin {

 |
|

3

3

 |

   late AnimationController _controller;

 |
|

4

 |

+  Alignment _dragAlignment = Alignment.center;

 |

Add a [GestureDetector](https://api.flutter.dev/flutter/widgets/GestureDetector-class.html) that handles the `onPanDown`, `onPanUpdate`, and `onPanEnd` callbacks. To adjust the alignment, use a [MediaQuery](https://api.flutter.dev/flutter/widgets/MediaQuery-class.html) to get the size of the widget, and divide by 2. (This converts units of "pixels dragged" to coordinates that [Align](https://api.flutter.dev/flutter/widgets/Align-class.html) uses.) Then, set the `Align` widget's `alignment` to `_dragAlignment`:

lib/{step1.dart (build) → step2.dart (build)} Viewed

|  |

@@ -1,8 +1,22 @@

 |
|

1

1

 |

  @override

 |
|

2

2

 |

  Widget build(BuildContext context) {

 |
|

3

 |

-  ~~return~~ ~~Align~~(

 |
|

4

 |

-  ~~child:~~ ~~Card~~(

 |
|

5

 |

-  ~~child~~: ~~widget.child~~,

 |
|

3

 |

+  var size = MediaQuery.of(context).size;

 |
|

4

 |

+  return GestureDetector(

 |
|

5

 |

+  onPanDown: (details) {},

 |
|

6

 |

+  onPanUpdate: (details) {

 |
|

7

 |

+  setState(() {

 |
|

8

 |

+  _dragAlignment += Alignment(

 |
|

9

 |

+  details.delta.dx / (size.width / 2),

 |
|

10

 |

+  details.delta.dy / (size.height / 2),

 |
|

11

 |

+  );

 |
|

12

 |

+  });

 |
|

13

 |

+  },

 |
|

14

 |

+  onPanEnd: (details) {},

 |
|

15

 |

+  child: Align(

 |
|

16

 |

+  alignment: _dragAlignment,

 |
|

17

 |

+  child: Card(

 |
|

18

 |

+  child: widget.child,

 |
|

19

 |

+  ),

 |
|

6

20

 |

   ),

 |
|

7

21

 |

   );

 |
|

8

22

 |

  }

 |

[](https://docs.flutter.dev/cookbook/animation/physics-simulation#step-3-animate-the-widget)Step 3: Animate the widget
----------------------------------------------------------------------------------------------------------------------

When the widget is released, it should spring back to the center.

Add an `Animation<Alignment>` field and an `_runAnimation` method. This method defines a `Tween` that interpolates between the point the widget was dragged to, to the point in the center.

lib/{step2.dart (animation) → step3.dart (animation)} Viewed

|  |

@@ -1,4 +1,5 @@

 |
|

1

1

 |

  class _DraggableCardState extends State<DraggableCard>

 |
|

2

2

 |

   with SingleTickerProviderStateMixin {

 |
|

3

3

 |

   late AnimationController _controller;

 |
|

4

 |

+  late Animation<Alignment> _animation;

 |
|

4

5

 |

   Alignment _dragAlignment = Alignment.center;

 |

content_copy

```
void _runAnimation() {
  _animation = _controller.drive(
    AlignmentTween(
      begin: _dragAlignment,
      end: Alignment.center,
    ),
  );
  _controller.reset();
  _controller.forward();
}
```

Next, update `_dragAlignment` when the `AnimationController` produces a value:

lib/{step2.dart (initState) → step3.dart (initState)} Viewed

|  |

@@ -3,4 +3,9 @@

 |
|

3

3

 |

   super.initState();

 |
|

4

4

 |

   _controller =

 |
|

5

5

 |

   AnimationController(vsync: this, duration: const Duration(seconds: 1));

 |
|

6

 |

+  _controller.addListener(() {

 |
|

7

 |

+  setState(() {

 |
|

8

 |

+  _dragAlignment = _animation.value;

 |
|

9

 |

+  });

 |
|

10

 |

+  });

 |
|

6

11

 |

  }

 |

Next, make the `Align` widget use the `_dragAlignment` field:

content_copy

```
child: Align(
  alignment: _dragAlignment,
  child: Card(
    child: widget.child,
  ),
),
```

Finally, update the `GestureDetector` to manage the animation controller:

lib/{step2.dart (gesture) → step3.dart (gesture)} Viewed

|  |

@@ -1,5 +1,7 @@

 |
|

1

1

 |

  return GestureDetector(

 |
|

2

 |

-  onPanDown: (details) {~~},~~

 |
|

2

 |

+  onPanDown: (details) {

 |
|

3

 |

+  _controller.stop();

 |
|

4

 |

+  },

 |
|

3

5

 |

   onPanUpdate: (details) {

 |
|

4

6

 |

   setState(() {

 |
|

5

7

 |

   _dragAlignment += Alignment(

 |
|  |

@@ -8,7 +10,9 @@

 |
|

8

10

 |

   );

 |
|

9

11

 |

   });

 |
|

10

12

 |

   },

 |
|

11

 |

-  onPanEnd: (details) {~~},~~

 |
|

13

 |

+  onPanEnd: (details) {

 |
|

14

 |

+  _runAnimation();

 |
|

15

 |

+  },

 |
|

12

16

 |

   child: Align(

 |
|

13

17

 |

   alignment: _dragAlignment,

 |
|

14

18

 |

   child: Card(

 |

[](https://docs.flutter.dev/cookbook/animation/physics-simulation#step-4-calculate-the-velocity-to-simulate-a-springing-motion)Step 4: Calculate the velocity to simulate a springing motion
--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

The last step is to do a little math, to calculate the velocity of the widget after it's finished being dragged. This is so that the widget realistically continues at that speed before being snapped back. (The `_runAnimation` method already sets the direction by setting the animation's start and end alignment.)

First, import the `physics` package:

content_copy

```
import 'package:flutter/physics.dart';
```

The `onPanEnd` callback provides a [DragEndDetails](https://api.flutter.dev/flutter/gestures/DragEndDetails-class.html) object. This object provides the velocity of the pointer when it stopped contacting the screen. The velocity is in pixels per second, but the `Align` widget doesn't use pixels. It uses coordinate values between [-1.0, -1.0] and [1.0, 1.0], where [0.0, 0.0] represents the center. The `size` calculated in step 2 is used to convert pixels to coordinate values in this range.

Finally, `AnimationController` has an `animateWith()` method that can be given a [SpringSimulation](https://api.flutter.dev/flutter/physics/SpringSimulation-class.html):

content_copy

```
/// Calculates and runs a [SpringSimulation].
void _runAnimation(Offset pixelsPerSecond, Size size) {
  _animation = _controller.drive(
    AlignmentTween(
      begin: _dragAlignment,
      end: Alignment.center,
    ),
  );
  // Calculate the velocity relative to the unit interval, [0,1],
  // used by the animation controller.
  final unitsPerSecondX = pixelsPerSecond.dx / size.width;
  final unitsPerSecondY = pixelsPerSecond.dy / size.height;
  final unitsPerSecond = Offset(unitsPerSecondX, unitsPerSecondY);
  final unitVelocity = unitsPerSecond.distance;

  const spring = SpringDescription(
    mass: 30,
    stiffness: 1,
    damping: 1,
  );

  final simulation = SpringSimulation(spring, 0, 1, -unitVelocity);

  _controller.animateWith(simulation);
}
```

Don't forget to call `_runAnimation()` with the velocity and size:

content_copy

```
onPanEnd: (details) {
  _runAnimation(details.velocity.pixelsPerSecond, size);
},
```

info Note: Now that the animation controller uses a simulation it's `duration` argument is no longer required.