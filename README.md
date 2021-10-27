# TcTransform

## Description

A TwinCAT 3 Library for 3D Coordinate System Transformations.

Features include:

* Vectors (3x1) : Represents a position in 3D space
* [Rotation Matricies](https://en.wikipedia.org/wiki/Rotation_matrix#In_three_dimensions) (3x3) : Represents a rotation in 3D space
* [Homogeneous Transformation Matricies](https://en.wikipedia.org/wiki/Transformation_matrix) (4x4) : Represents a coordinate system in 3D space
* [Euler Angles](https://en.wikipedia.org/wiki/Euler_angles#Proper_Euler_angles_2) and other [angle-sequence representations](https://en.wikipedia.org/wiki/Euler_angles#Tait%E2%80%93Bryan_angles_2)

Compare to:

*  [OSCAT](http://www.oscat.de/) Vector3 ([docs](http://www.oscat.de/component/jdownloads/download/2-oscat-basic/9-oscat-basic333-en.html))

## TwinCAT 3 Installation

### From Binary

1. Go to the [releases page](https://github.com/BurksEngineering/TcTransform/releases) and download the *.library file from the latest release, saving it to a temporary directory somewhere on the computer running TwinCAT.
1. Follow the [library installation instructions](https://infosys.beckhoff.com/english.php?content=../content/1033/tc3_plc_intro/4189333259.html) from Beckhoff, using the 'Install' option and browsing for the local copy you had just downloaded.
1. After installtion, you may delete the local copy of the library that you had downloaded. Beckhoff has likely copied it to a version-specific folder in `C:\TwinCAT\3.1\Components\Plc\Managed Libraries\Burks\TcMatrix\`

### From Source

Preconditions:

* TwinCAT 3.1.4024 XAE
* TwinCAT 3.1.4024 XAR, ideally configured with an isolated core
* Visual Studio Community or Shell, 2019 or higher
* [TcUnit](https://github.com/tcunit/TcUnit) v1.2 or higher
* [TcMatrix](https://github.com/BurksEngineering/TcMatrix) : Matrix math library

Compilation:

1. Clone the repository to a computer running TwinCAT 3
1. Open the [main solution file](TcTransform.sln) in Visual Studio
1. Within the Visual Studio *TcTransform* project, right click on the *TcTransform/TcTransform Project* item and select *Rebuild*
   1. There should be no compile errors, although there may be warnings
1. Right click on the *TcTransform/TcTransform Project* item again and select *Save as library and install*
   1. The file can be saved anywhere, but a local *Builds* folder is always a good choice

Test:

1. Within the Visual Studio *TcTransformTest* project, right click on the *PLC/TcTransformTest/TcTransformTest Project* item and select *Rebuild*
   1. There should be no compile errors, although there may be warnings
1. Open the *SYSTEM/Real-Time* item and click *Read From Target*, then assign all tasks to an isolated core from your system
1. In the Visual Studio toolbar (or TwinCAT file menu) click *Activate Configuration*
1. When prompted, agree to start the project in run mode and auto-start the boot project
1. After a few seconds the TcUnit results should be visible in the Error List window. Verify that all tests have passed.

## Library Components

### Vector3 Class

This class is a 3x1 column vector with X, Y, and Z components.
It derives from the base TcMatrix.Matrix FB and inherits all of that base functionality, but its size is fixed at 3x1.
Unlike the base Matrix FB, instances of Vector3 can be assigned (`:=`) or returned from functions directly by value (no internal pointers!).

#### Initialization

These methods all clear the previous contents of the vector and set it to the new values.

* __FB_init__ : When initialized by TwinCAT (called implicitley) the vector sets the value of all three components (X,Y,Z) to 0.0
* __Clear()__ : (From base class) Resets all three components (X,Y,Z) to 0.0;
* __SetFromComponents(X,Y,Z)__ : Allows initializing all three components (X,Y,Z) in once line
* __SetAsCrossProduct(A,B)__ : Allows initializing the vector as the cross product of two other vectors (A x B)

#### Properties

* __X__ : Get or set the first element (I=0) of the vector 
* __Y__ : Get or set the second element (I=1) of the vector
* __Z__ : Get or set the third element (I=2) of the vector

#### Modifiers

These methods are used to alter the value of an existing vector.

* __Reverse()__ : Equivalent to scaling the vector by -1, this reverses the value of each element of the vector
* __SumWith(V)__ : Sets each element of this vector to the sum of its current value and the value of its corresponding element in the supplied vector
* __RotateBy(R)__ : Rotates the current vector out of the supplied rotation frame
* __RotateByReverse(R)__ : Rotates the current vector into the supplied rotation frame
* __TransformBy(H)__ : Transforms the current vector out of the supplied coordinate system
* __TransformByReverse(H)__ : Transforms the current vector into the supplied coordinate system

#### Getters

These methods are used to return a new vector based on the values of the current vector and other supplied objects.

* __GetScaled(S)__ : Returns a vector where each element as been scaled by the supplied LREAL
* __GetReversed()__ : Returns a vector where each element has been scaled by -1
* __GetVector3Sum(V)__ : Returns a vector where each element is the sum of the corresponding values in this vector and the supplied vector
* __GetVector3Difference(V)__ : Returns a vector where each element is the sum of the corresponding values in this vector (as minuend) and the supplied vector (as subtrahend)
* __GetVector3DotProduct(V)__ : Returns an LREAL that is the dot product of this vector and the supplied vector
* __GetVector3CrossProduct(V)__ : Returns a vector that is the cross product of this vector and the supplied vector

### RMatrix (Rotation Matrix) Class

This class is a 3x3 matrix that describes a rotation in 3D.
There are a ton of resources available to learn more about [3D rotation matricies](https://en.wikipedia.org/wiki/Rotation_matrix#In_three_dimensions) and help develop an intuition for them.

