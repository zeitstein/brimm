# Block Properties

Properties model individual pieces of data (e.g. for an album: the release date, the genres, the songs), defining the type of their values (a date, multiple enumerated values, nested blocks).

Properties exist independently of blocks and block types. They can be created directly from blocks or through the properties side panel.

You can add any property to any block. Adding properties to blocks is done through the [block prop table](user-guide.md#ui-block-components). There is also a keyboard shortcut and a mouse-based shortcut on the button which toggles the prop table.

All properties have an **id**, a **type**, a **name** and a **description**. There are also attributes specific to certain prop types.

## Property (value) types:

A property's type determines:
- the types of values the property may take
- the UI widget used to edit the value
- the kinds of [query conditions](queries-and-views.md#view-options) that can target them

The block value of any property may be `nil` meaning "not set".

> [!TIP]
> Check tooltips of prop attributes in the properties side panel for more info.

### blocks

This prop type models parent-child block relationships, i.e. blocks containing other blocks (like nesting in traditional outliners). A block may be in multiple locations i.e. inside multiple parent blocks. The only limitation: a block may be in a single parent blocks *prop* only once. The term "add" is used as a shorthand for "add to location" throughout the app.

You may define [views](queries-and-views.md#block-query) on them.

Type-specific attributes: `component?`, `blocks type`, `views`.

Value: `vector` of block ids.

### rich-text

The UI widget is a rich-text editor.

> [!TIP]
> By default, `Shift-Enter` creates a new *line* inside the editor, while `Enter` creates a new sibling *block*.

Formatting the text is currently only available through markdown-like keyboard shortcuts. Available formatting and shortcuts:
- inline:
  - bold: `**`, `__`
  - italic: `*`, `_`
  - strikethrough: `~~`
  - inline code: `` ` ``
  - links: `[text](url)`; may also create links to Brimm blocks by dragging a block onto the editor (at inline-level)
  - highlight: `==`
  - math (LaTeX): `$`
- block-level:
  - paragraph
  - headings: `#`, `##`, `###`, `####`
  - blockquote: `>`
  - code blocks: `` ```(language) ``
  - lists:
    - bullet: `-`
    - numbered: `1.`
    - todo: `[]`
    - toggle: `>>`
  - images: drag an image into the editor (at block-level)
  - math (LaTeX): `$$`
  - embedding (transcluding) other Brimm blocks - drag a block onto the editor at (block-level)

On typing `@` a typeahead will appear through which you can search for blocks to insert links to. If at the start of an empty editor, you may hold down `Ctrl/Cmd` to replace the current block with the selected one (the selected one will be added to this location, not moved).

Value: `vector` whose first element is the HTML string representing the rich-text, while the rest are block or user ids.

This somewhat weird heterogeneous vector value was chosen because:
- XTDB indexes each item in a vector individually, enabling efficient querying of references contained within the rich-text.
- The HTML string will be indexed by Lucene for full-text search.

### text

Values are plain text strings. Compared to `rich-text`, useful when you want to enforce plain text strings as values.

Value: `string`.

### URL

Value: `string`.

### number

Supports decimals. Currently no formatting options.

Value: `int` or floats.

### date

Value: `java.time.Instant` (UTC)

### datetime

Value: `java.time.Instant` (UTC)

### select

Choose one of a set of user-defined values. The values, called **enums**, have labels and background colors and can be ordered (by dragging).

Type-specific attributes: `enums`.

Value: enum id.

### multiselect

Same as select except multiple choice.

Value: `vector` of enum ids.

### checkbox

Value: `true` or `false`

### users

Multiple choice of [users](user-guide.md#users).

Value: `set` of user ids.

### block-query

Type-specific attrs: `views`.

Enables queries across the database. Discussed in-depth [here](queries-and-views.md#block-query).

Value: views `map` (see `brimm.model.spec/Views`).

### custom

Type-specific attrs: `render-script`, `read-script`, `tx-script`.

The user [programs](user-guide.md#programmability) how the prop values are computed (`read-script` or `tx-script`) and rendered (`render-script`) through SCI (Clojure) scripts.

> [!TIP]
> Check the tooltips of the `*-script` attributes in the properties side panel for more info.

Value:
- for custom props with a
  - `tx-script`: whatever is returned from the script
  - `read-script`: `nil` (the values are computed when needed and not persisted)
- for both:
  - the underlying value may be edited through the UI
  - Brimm may store the width of the UI widget if resized (and possible) in `[:brimm.render/val-data :width]`

> [!TIP]
> If wanting to render URLs (as images, videos, youtube embeds, etc.) return `:brimm.id/url-renderer` from your `render-script`. This will render the prop value with `brimm.client.ui.prop-val.core/ui-url-renderer`.

### server-tx

Type-specific attrs: `server-tx script`, `server-tx label`.

The user defines a SCI script which runs on the server to produce data to be transacted to the database. The value UI widget is a button which triggers the script with `server-tx label`.

> [!TIP]
> Check the tooltip of the `server-tx script` attribute in the properties side panel for more info.

Value: `nil`.

> [!TIP]
> The built-in bookmark type provides a nice example of this prop type: its server-tx script fetches bookmark info via an HTTP request and changes the layout of the block.

## Built-in props

You can find the list of built-in props in the properties side panel. Some are "system props", some are used by the [built-in block types](block-types.md#built-in-types), while others should be generally useful (e.g. using the `name` prop for the names of your blocks).

The built-in props shouldn't be deleted or their ids changed. You may modify the rest of their attributes through the UI.
