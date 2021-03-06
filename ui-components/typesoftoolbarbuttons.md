---
layout: default
title: Types of toolbar buttons
title_nav: Types of toolbar buttons
description: This section demonstrates different types of toolbar buttons.
keywords: toolbar toolbarbuttons buttons toolbarbuttonsapi
---

There are four types of Toolbar Buttons in TinyMCE 5.0:

* Basic button
* Toggle button
* Split button
* Menu button

### Basic button

A basic button triggers its `onAction` function when clicked.

#### Config options

| Name | Value | Requirement | Description |
| ---- | ----- | ----------- | ----------- |
| text | string | optional | Text to display if no icon is found. |
| icon | string | optional | Name of the icon to be displayed. Must correspond to an icon in the icon pack. |
| tooltip | string | optional | Text for button tooltip.  |
| disabled | boolean | optional | default: false - Represents the button's state. When true, button is unclickable. Toggled by the button's API. |
| onSetup | (api) => (api) => void | optional | default: () => () => {} - Function invoked when the button is rendered. |
| onAction | (api) => void | required | Function invoked when the button is clicked. |

> Note: See [below]({{site.baseurl}}/ui-components/typesoftoolbarbuttons/#onSetupexplanation) for details on how to configure `onSetup`.

#### API

| Name | Value | Description |
| ---- | ----- | ----------- |
| isDisabled | () => boolean | Checks if the button is disabled. |
| setDisabled | (state: boolean) => void | Sets the button's disabled state. |


#### Basic button example and explanation

The following example adds two buttons to the toolbar:

{% include codepen.html id="custom-toolbar-button" tab="js" %}

The first button inserts "It's my button!" into the editor when clicked. The second button is an example of how `onSetup` works. This button inserts a `time` element containing the current date into the editor using a `toTimeHtml()` helper function - a simplified version of TinyMCE's [insertdatetime]({{site.baseurl}}/plugins/insertdatetime/) plugin.

In this example an icon from the `insertdatetime` plugin is used to demonstrate how to use a registered icon. `disabled` is set to `true` so that the button is disabled when it is first rendered.

`onSetup` is used to listen to the editor's [`NodeChange` event]({{site.baseurl}}/advanced/events/#nodechange) to disable the button when the cursor is inside a `time` element (or "node"). This ensures it is not possible to insert a `time` element into another `time` element.


### Toggle button

A toggle button triggers an action when clicked but also has a concept of state. This means it can be toggled `on` and `off`. A toggle button gives the user visual feedback for its state through CSS styling. An example of this behavior is the **Bold** button that is highlighted when the cursor is in a word with bold formatting.

#### Config options

| Name | Value | Requirement | Description |
|------| ------| ------------| ----------- |
| text | string | optional | Text to display if no icon is found. |
| icon | string | optional | Name of the icon to be displayed. Must correspond to an icon in the icon pack. |
| tooltip | string | optional | Text for button tooltip.  |
| disabled | boolean | optional | default: false - Represents the button's state. When true, button is unclickable. Toggled by the button's API. |
| active | boolean | optional | default: false - Represents the button's state. When true, button is highlighted. Toggled by the button's API. |
| onSetup | (api) => (api) => void | optional | default: () => () => {} - Function invoked when the button is rendered. |
| onAction | (api) => void | required | Function invoked when the button is clicked. |

> Note: See [below]({{site.baseurl}}/ui-components/typesoftoolbarbuttons/#onSetupexplanation) for details on how to configure `onSetup`.

#### API

| Name | Value | Description |
|------| ------| ------------|
| isDisabled | ( ) => boolean | Checks if a button is disabled. |
| setDisabled | (state: boolean) => void | Sets the button's disabled state. |
| isActive | ( ) => boolean | Checks if the button is `on`. |
| setActive | (state: boolean) => void | Sets the button's toggle state. |

#### Toggle button example and explanation

{% include codepen.html id="custom-toolbar-toggle-button" %}

The example above adds two custom **strikethrough** buttons with the same `onAction` configuration. The configuration uses `editor.execCommand(command, ui, args)` to execute `mceToggleFormat`. This editor method toggles the specified format on and off, but only works for [formats]({{site.baseurl}}/configure/content-formatting/#formats) that are already registered with the editor. In this example, `strikethrough` is the registered format.

The first button applies and removes strikethrough formatting, and its state toggles on click using `api.setActive(!api.isActive())`. However, the expected behaviour is that the button's state will reflect whether the selected content has strikethrough formatting. For example, if the cursor is moved into editor content that has strikethrough formatting the button will become **active** and if it is moved into content that does not have strikethrough formatting the button will become **inactive**. The first button in the example does not do this, since its state only toggles when the button is clicked.

To achieve this, the second button uses `onSetup` to register a callback for strikethrough content using `editor.formatter.formatChanged(formatName, callback)`. This method executes the specified callback function when the selected content has the specified formatting.

> Note: The format name given to `mceToggleFormat` via `editor.execCommand(command, ui, args)` and to `editor.formatter.formatChanged(formatName, callback)` is the same.

The callback given to `editor.formatter.formatChanged` is a function that takes a `state` boolean representing whether the currently selected content contains the applied format. This `state` boolean is used to set the button's active state to match if the selected content has the specified formatting by using `api.setActive(state)` from the button's API. This ensures the `customToggleStrikethrough` button is only **active** when the selected content contains the strikethrough formatting.

### Split button

A split button contains a basic button and a menu button, wrapped up into one toolbar item. Clicking the menu button section opens a dropdown list. The basic button section and the menu items can be configured to trigger different actions when clicked.

> Example: Font select dropdown.

#### Config options

| Name | Value | Requirement | Description |
|------| ------| ------------| ----------- |
| text | string | optional | Text displayed if no icon is found. |
| icon | string | optional | Name of the icon to be displayed. Must correspond to an icon in the icon pack. |
| select | (value: string) => boolean  | optional | default: false - Function run on each option to determine if it should be highlighted as active. |
| columns | string | optional | default: 1 - Number of columns for the list of options. |
| fetch | (success: (menu) => void) => void  | required | Function that takes a callback which must be passed the list of options for the button's dropdown. |
| onAction | (api) => void | required | Function invoked when the basic button section is clicked. |
| onItemAction | (api, value) => void | required | Function invoked when a dropdown list option is clicked. |
| onSetup | (api) => (api) => void | optional | default: () => () => {} - Function invoked when the button is rendered. |

> Note: See [below]({{site.baseurl}}/ui-components/typesoftoolbarbuttons/#onSetupexplanation) for details on how to configure `onSetup`.

#### API

| Name | Value | Description |
|------| ------| ------------|
| isDisabled | ( ) => boolean | Checks if button is disabled. |
| setDisabled | (state: boolean) => void | Sets the button's disabled state. |
| isActive| ( ) => boolean | Checks the button's toggle state. |
| setActive | (state: boolean) => void | Sets the button's toggle state. |

#### Split button example and explanation

The following example sets up a split button with a text label and a static dropdown menu.

{% include codepen.html id="custom-toolbar-split-button" tab="js" %}

A split button is similar to a basic button in that they both require an `onAction` callback. The `onAction` callback function should take the button's API and return nothing. It is called when the basic button section is clicked. In this example, `onAction` calls `editor.insertContent(value)` which inserts the given content into the editor.

`onItemAction` is called when a menu item is clicked. The callback function is passed the split button's API and the *value* of the selected menu item. Nothing should be returned. The example calls `editor.insertContent(value)` to insert the *value* into the editor's content.

The `fetch` function is called whenever the split button's drop-down menu is opened. It is a function that takes a callback and passes it an array of menu items to be rendered in the button's drop-down menu. This allows for asynchronous fetching of the menu items.

### Menu button

A toolbar menu button is a toolbar button that opens a menu when clicked. This menu can also contain submenus. This is useful for grouping together actions that would otherwise be several buttons on the toolbar. It can also be used to reduce visual clutter and save UI space, as menubar menu items and some toolbar buttons could be moved into a toolbar menu button. Potentially, all menubar menu items could be moved into toolbar menu buttons, allowing for the editor to be used without a menubar at all.

> Example: The table plugin's `table` toolbar button opens a menu similar to the menubar Table menu.

#### Config options

| Name | Value | Requirement | Description |
|------| ------| ------------| ----------- |
| text | string | optional | Text to display if no icon is found. |
| icon | string | optional | Name of the icon to be displayed. Must correspond to an icon in the icon pack. |
| tooltip | string | optional | Text for button tooltip. |
| fetch | (success: (menu) => void) => void  | required | Function that takes a callback which must be passed the list of options for the button's dropdown. |
| onSetup | (api) => (api) => void | optional | default: () => () => {} - Function that's invoked when the button is rendered. |

> Note: See [below]({{site.baseurl}}/ui-components/typesoftoolbarbuttons/#onSetupexplanation) for details on how to configure `onSetup`.

#### API

| Name | Value | Description |
|------| ------| ------------|
| isDisabled | ( ) => boolean | Checks if the button is disabled. |
| setDisabled | (state: boolean) => void | Sets the button's disabled state. |

#### Menu button example and explanation

The following is a simple toolbar menu button example:

{% include codepen.html id="custom-toolbar-menu-button" tab="js" %}

This example configures a toolbar menu button with the label `My Button` that opens the specified menu when clicked. The top-level menu contains two items. The first menu item inserts content when clicked and the second menu item opens a submenu containing two menu items which insert content when clicked.

The `fetch` function is called when the toolbar menu button's menu is opened. It is a function that takes a callback and passes it an array of menu items to be rendered in the drop-down menu. This allows for asynchronous fetching of the menu items.

Use the following demo [here]({{site.baseurl}}/demo/custom-toolbar-menu-button/) for help using the menu toolbar button.

### `onSetup` explanation

{% include release-notes/onSetup.md %}
