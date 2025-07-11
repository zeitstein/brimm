# Block Types

You may think of a block type as:
- an inherent category ("This is a person.")
- a blueprint from which members of the category are instantiated.

After instantiation, you may change anything about the instance blocks. They will still be "tagged" as being of that type. (A person remains a person even you forget all the information about them or that they're supposed to have a name.) Unless you remove the "tag" itself. ("Maybe this wasn't a person after all!")

Block types have an **id**, a **name**, a **description** and a **prototype**.

## Prototype

The **prototype** is just a block that is used as a template when types are instantiated (i.e. when a block of a certain type is created). You work with prototypes as you do with any block.

More precisely, the prototype is the root of a tree of **template** blocks associated to a type.

Unlike 'regular' blocks, you may set **template markers** as a template's prop's values (and in certain other places, like query conditions) through the prop menu in the block props table. These markers are then targeted in scripts which run during **type instantiation**.

Type instantiation steps:
- creating a new tree of blocks by copying the template tree
- for each new instance (if corresponding scripts are present):
  - replace markers with system markers and markers provided by the `init-markers-script`
  - replace the result with the result of the template `init-script`

> [!TIP]
> More on these two scripts in the tooltips where they are defined in [Block Info](user-guide.md#block-info).

> [!TIP]
> See the calendar day block type in `brimm.model.base-data` as an example for prototypes and using template markers.

An additional prototype feature is **propagating changes** to their existing instances.
Example: change the icon for all blocks of a certain type.

Propagating changes can be done automatically and is turned on by default after creating a new type. This is mostly useful during initial tweaking of the prototype (I recommend turning it off after). You can still manually propagate individual changes through the prop menu and convenient buttons in [Block Info](user-guide.md#block-info).

## Built-in types

You can find the list of built-in types in the block types side panel.

You may modify the prototypes of built-in types, but cautiously as it may cause some features to break. Strong recommendations:
- Don't modify the file prototype, except maybe the visuals.
- Calendar days need to have the correct built-in `calendar date` prop on them.
- A message should have the built-in `messages` prop to enable threads.
