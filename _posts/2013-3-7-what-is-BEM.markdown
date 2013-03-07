---
layout: post
categories: javascript
title: BEM methodology
---

什么是BEM
---------
A block is a part of page design or layout whose specific and unique meaning is defined either semmantically or visually.

A block consists of elements.

The core distinction between a block and an element is an element's inability to exist outside of its parent block's context. If something coundn't be detached from a block, It was an element; detachable elements(probably) should themselves be blocks.

BLOCKS: DECLARATION OF INDEPENDENCE

1. only class names(not ids) should be used for css
2. each block's class name should have a namespace(prefix)
3. every css rule must belong to a block


SIMPLE AND COMPOUND BLOCKS: THE MISCLASSIFICATION

any block should allow for arbitrary content to be embedded, whenever possible

COMPLETE INDEPEDENT BLOCKS

A complete indepedent block, has the following rules added:

1. never match css with tag names. use class names for everything
   .b-user b ==> .b-user .first-letter
2. class names for block elements must be prefixed with the parent's block name
   .b-user .first-letter ==> .b-user-first_letter


PREFIXES
--------

* b-
  Common blocks
* h-
  Holster, used to glue several elements together
* l-
  Layout grids
* g-
  Global styles

MODIFIERS
---------

A "modifier" can be defined as a particular state of a block, a flag holding a specific property. For example, a block representing a button could have three default sizes: small, normal and big. Instend of creating three different blocks, you would assign a modifier to the block. The modifier would require a name(for example, size) and a value(small, normal or big).

There are two reasons for a block to change its presentation state:

* A block’s presentation could be altered because of its placement in the layout. This was called a “context-dependent” modification.
* An additional (postfixed) class name could change a block’s appearance by applying extra CSS rules. This was a “context-independent” modifier.
  class="b-block b-block-postfix"



