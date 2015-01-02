GLAArrayEditing
===============

## Convenient and powerful API, simple implementation

If you need a reorderable list of items in your application, you can use a raw `NSMutableArray`, implement the key-value-observing compatible methods, or write custom methods to perform the same concepts again and again of adding, removing, replacing, and reordering.

The **GLAArrayEditing** protocol allows you to present a powerful, consistent, and safe interface for any array of items in your public API. Behind the scenes **GLAArrayEditor** allows your implementation to be just two methods: one to copy items and one to edit.

### Get changes

Use the `-changesMadeInBlock:` method of `GLAArrayEditor` to track the added, removed, or replaced objects. Its return value provides access these combined changes as simple properties.

### Indexers

Indexers allow array items to be looked up quickly by key. Any changes to the array update the index efficently.

### Stores

The `GLAArrayEditor` class supports stores, so your list of items can be loaded from disk, and also automatically saved after any changes. For using Mantle objects stored as JSON, see the [GLAArray-Mantle respository.](https://github.com/BurntCaramel/GLAArray-Mantle)

### Table View Dragging

Use the `GLAArrayTableDraggingHelper` to easily enable rearranging in your `<GLAArrayEditing>`-supported table data source.

You can easily create your own classes that conform to the `GLAArrayEditing` protocol.

## Example

### Your public model API with two methods

```objective-c
@interface ExampleObjectWithListOfCollections : NSObject

- (NSArray *)copyCollections;
- (void)editCollectionsUsingBlock:(void (^)(id<GLAArrayEditing> collectionListEditor))block;
	
@end
```

### Your implementation

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

## The GLAArrayEditing methods

```objective-c
@protocol GLAArrayInspecting <NSObject>

@property(readonly, nonatomic) NSUInteger childrenCount;

- (NSArray *)copyChildren;
- (id)childAtIndex:(NSUInteger)index;
- (NSArray *)childrenAtIndexes:(NSIndexSet *)indexes;

- (NSArray *)resultsFromChildVisitor:(GLAArrayChildVisitorBlock)childVisitor;

// Working with unique children
- (id)firstChildWhoseKey:(NSString *)key hasValue:(id)value;
- (NSUInteger)indexOfFirstChildWhoseKey:(NSString *)key hasValue:(id)value;
- (NSIndexSet *)indexesOfChildrenWhoseResultFromVisitor:(GLAArrayChildVisitorBlock)childVisitor hasValueContainedInSet:(NSSet *)valuesSet;

- (NSArray *)filterArray:(NSArray *)objects whoseResultFromVisitorIsNotAlreadyPresent:(GLAArrayChildVisitorBlock)childVisitor;

@end


@protocol GLAArrayEditing <GLAArrayInspecting>

- (void)addChildren:(NSArray *)objects;
- (void)insertChildren:(NSArray *)objects atIndexes:(NSIndexSet *)indexes;
- (void)removeChildrenAtIndexes:(NSIndexSet *)indexes;
- (void)replaceChildrenAtIndexes:(NSIndexSet *)indexes withObjects:(NSArray *)objects;
- (void)moveChildrenAtIndexes:(NSIndexSet *)indexes toIndex:(NSUInteger)toIndex;

- (void)replaceAllChildrenWithObjects:(NSArray *)objects;

- (BOOL)removeFirstChildWhoseKey:(NSString *)key hasValue:(id)value;

- (id)replaceFirstChildWhoseKey:(NSString *)key hasValue:(id)value usingChangeBlock:(id (^)(id originalObject))objectChanger;

@end
```

## Source

This was developed during the creation of my app [Blik](http://twitter.com/BlikApp).
