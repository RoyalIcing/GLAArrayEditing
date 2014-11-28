GLAArrayEditing
===============

## Use

If you have a reorderable list of items in your application, you can use a raw NSMutableArray, key-value-observing compatible methods, or write custom methods to perform the same concepts of adding, removing, replacing, and reordering.

The GLAArrayEditing protocol allows you to present a powerful, consistent, and safe interface for any array of items. Behind the scenes GLAArrayEditor allows your implementation to be just two methods, one to copy and one to edit. You can also create intermediate or your own classes that conform to the protocol.

Use the edit method of GLAArrayEditor to track changes such as added, removed or replaced objects, using return value to access these combined changes as simple properties.

## Your visible API with two methods

```objective-c
@interface ExampleObjectWithListOfCollections : NSObject

- (NSArray *)copyCollections;
- (void)editCollectionsUsingBlock:(void (^)(id<GLAArrayEditing> collectionListEditor))block;
	
@end
```

## Your implementation

```objective-c
@interface ExampleObjectWithListOfCollections ()

@property(nonatomic) GLAArrayEditor *collectionsArrayEditor;

@end

@implementation ExampleObjectWithListOfCollections

- (NSArray *)copyCollections
{
	return [(self.collectionsArrayEditor) copyChildren];
}

- (void)editCollectionsUsingBlock:(void (^)(id<GLAArrayEditing> collectionListEditor))block
{
	GLAArrayEditor *arrayEditor = (self.collectionsArrayEditor);
	GLAArrayEditorChanges *changes = [arrayEditor changesMadeInBlock:block];
	
	// Now respond to the changes made:
	
	// changes.addedChildren
	
	// changes.removedChildren
	
	// changes.replacedChildrenBefore
	// changes.replacedChildrenAfter
}

- (instancetype)init
{
    self = [super init];
    if (self) {
        _collectionsArrayEditor = [GLAArrayEditor new];
    }
    return self;
}

@end
```

## Source

This was developed during the creation of the app [Blik](http://twitter.com/BlikApp).