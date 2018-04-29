---
layout: post
title: Linking Objective-C Libraries to Swift Frameworks
tags: [xcode, framework]
---

Recently I was trying to link a static objective-c library to a swift framework. My initial thought was I needed to create a bridging header. However, after reviewing the  [Mix and Match](https://developer.apple.com/library/content/documentation/Swift/Conceptual/BuildingCocoaApps/MixandMatch.html) documentation, it was clear that only app targets support bridging headers, frameworks need to use an umbrella header. But there is a catch. The frameworks/libraries you are attempting to import need to have a module defined. I was out of luck, I had a binary library file and a bunch of headers.

### Creating a custom Module Map

I came across this [article](http://blog.bjhomer.com/2015/05/defining-modules-for-custom-libraries.html) by B.J. Homer that does a great job explaining how to create a custom module map.

In summary, you create a *modulemap.map* file that lists all of the header files part of the library.

```ruby
module MyFrameworkKit {
   header "MyHeader1.h"
   header "MyHeader2.h"
   header "MyHeader3.h"
   header "MyHeader4.h"
}
```

or alternatively, you can create a umbrella header *(eg. MyFrameworkKit.h*, which is a new header that that will import all of the headers.

```smalltalk
#import "MyHeader1.h"
#import "MyHeader2.h"
#import "MyHeader3.h"
#import "MyHeader4.h"
```
The revised module map utilizing the umbrella header will be as follows;
```ruby
module MyFrameworkKit {
   umbrella header "MyFrameworkKit.h"
   export *
}
```

The export * implies that all imported headers in the umbrella header to be part of the module.

Finally you need to place this newly created module map in the same directory as the header files

### Linking and Search Paths

Once you have the module map in place, you need to update a few build settings

1. Link the library to the appropriate target (app or framework). Complete this by dragging the binary library file into *Linked Frameworks and Libraries* section under the general tab in build settings

2. With your target selected, under build settings, add the Library path to the following

	**Import Paths** 
	```
	$(PROJECT_DIR)/Vendors/MyFrameworkKit/
	```
	![Private Module]({{ "assets/img/import_path.png" | absolute_url }})
	
Do the same for **Header Search Paths** and **Library Search Paths**

Finally add the module map file location to **Private Module Map File**

![Private Module]({{ "assets/img/private_module.png" | absolute_url }})

Once this is completed you should be able to use *import MyFrameworkKit* anywhere in your swift target.

Hope this helps.
