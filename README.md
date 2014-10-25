GLAArrayEditor
==============

## Use

If you have a reorderable list of items in your application, you can use a raw NSMutableArray, key-value-observing compatible methods, or write custom methods to perform the same concepts of adding, removing, replacing, and reordering.

The GLAArrayEditing protocol allows you to present a powerful yet safe interface for any array of items. Behind the scenes GLAArrayEditor allows your implementation to be just two methods, one to copy and one to edit.

The edit method of GLAArrayEditor tracks changes, such as added, removed or replaced objects, and provides access to them as simple properties for your code to work with.

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
	
	// changes.removeChildren
	
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