---
layout: post
title: Core Data and Ordered Relationships
tags: [coredata]
---
Prior to iOS 5 if you wanted to maintain a particular order in a core data relationship, you had to create an additional index attribute for the entity and track this value manually.  This was a royal pain and prone to errors. [NSOrderedSet](https://developer.apple.com/library/ios/documentation/Foundation/Reference/NSOrderedSet_Class/index.html) was introduced in iOS 5, it gives the benefit of items having an index like an array, but also having the added benefit of guaranteeing  all items are unique

Even though NSOrderedSet has been around for a while, I couldn’t find any concrete examples of how this worked in a modern Swift application. What I did find, was many cases where NSOrderedSet was not behaving or working as expected. It seems like the crux of the problem was when you create a NSManagedObject subclass the generated accessors were not implemented correctly or not implemented at all.

The only real solution is to create your own accessors in the NSManagedObject subclass. In order to do this we access a mutable copy of the ordered set using
`mutableOrderedSetValueForKey(_:)`
> Returns a mutable ordered set that provides read-write access to the uniquing ordered to-many relationship specified by a given key.

So, we can declare a private method in our NSManagedObject subclass to return a NSMutableOrderedSet.
{% highlight objc %}
@NSManaged public private(set) var subItems: NSOrderedSet

private var mutableSubItems: NSMutableOrderedSet {
   return mutableOrderedSetValueForKey("subItems")
}
{% endhighlight %}
Then we can create the necessary accessors like so;
{% highlight objc %}
public func indexOfObject(item: SubItem) -> Int {
    return mutableSubItems.indexOfObject(item)
}

public func insertObject(item: SubItem, index: Int) {
    mutableSubItems.insertObject(SubItem, atIndex: index)
}

public func moveObject(item: SubItem, indexes: NSIndexSet, toIndex: Int) {
    mutableSubItems.moveObjectsAtIndexes(indexes, toIndex: toIndex)
}
{% endhighlight %}

This is just a small sample of the accessors you can create, you can review all the methods available in [NSMutableOrderedSet](https://developer.apple.com/library/ios/documentation/Foundation/Reference/NSMutableOrderedSet_Class/index.html) to create more.
