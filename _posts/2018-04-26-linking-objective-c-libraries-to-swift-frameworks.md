---
layout: post
title: Linking Objective-C Libraries to Swift Frameworks
---

Recently I was trying to link a static objective-c library to a swift framework. My initial though was I needed to create a bridging header. However, after reviewing the  [Mix and Match](https://developer.apple.com/library/content/documentation/Swift/Conceptual/BuildingCocoaApps/MixandMatch.html) documentation, it was clear that only app targets support bridging headers, frameworks need to use a umbrella header. But there is a catch. The frameworks/libraries you are attempting to import need to have a module defined. I was out of luck, I had a binary library file and a bunch of headers.

## Creating a custom Module Map

I came across this [article](http://blog.bjhomer.com/2015/05/defining-modules-for-custom-libraries.html) by B.J. Homer that does a great job explaining how to create a custom module map.

In summary, you create a *modulemap.map* file that lists all of the header files you are importing. Optional you can create a umbrella header that import all of the headers.

**Show modulemap** example

1. Add the module map to the same directory as the header files.
2. Link the library to the appropriate target (app or framework)
3. Add settings
	- Import path
	- Header search paths
	- Library search paths
	- Private module map (directly to file)

