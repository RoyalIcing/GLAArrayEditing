GLAArrayEditor
==============

## Use

If you have a reorderable list of items in your applications, you can use a raw NSMutableArray, key-value-observing compatible methods, or simply custom methods. Methods to add, remove, replace, and reorder have to be wrapped or observed in order to give them an abstracted interface that is safe to use.

The GLAArrayEditing protocol allows you to present a safe interface to the methods that interact with your list of items, while having very straight forward observing of GLAArrayEditor behind the scenes, all in the implementation of the method.

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
	GLAArrayEditor *arrayEditor = (self.collectionsArrayEditor);
	return [arrayEditor copyChildren];
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

@end
```