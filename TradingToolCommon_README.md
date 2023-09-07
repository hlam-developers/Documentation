
# TradingToolCommon
This library is the backbone of almost every application currently being used.

## Installation
The library is currently being consumed by all TradingTools through Dynamic Link Library (dll) references. This library will soon be moved to be consumed through a Nuget package.

## Documentation
## TradingToolCommon.Algorithm
## TradingToolCommon.Blazor
### Components
### AutoCompleteInput

This component represents an input box that creates suggestions based on previously saved answers.

**Parameters**:

- `ActiveItemIndex` `int`: the current index number of the active item.
- `AutoCompleteItems` `List<IAutoCompleteItem<TItem>>`: the current list of suggestion objects in accordance with user input.
- `CanAddNew` `bool`: whether the user is able to create a new suggestion.
- `CanRemove` `bool`: whether the user is able to remove a suggestion.
- `CurrentSearchExactMatch` `bool`: whether the user's input text exactly matches a suggestion.
- `DropdownVisible` `bool`: whether the dropdown list displaying suggestions should be visible.
- `Filter` `AutoCompleteFilter`: sets the way suggestions appear to the user.
  - This is currently defaulted to `AutocompleteFilter.Contains` i.e. suggestions appear as long as the user's typed text is contained within the suggestion.
- `Focused` `bool`: whether the input box is currently in focus.
- `InputText` `string?`: the text the user has inputted.
- `ItemRemoved` `EventCallback<TItem>`: function handling the removal of a suggestion.
- `MinLength` `int`: the minimum number of characters a user must type before suggestions appear.
- `NewItemAdded` `EventCallback<string>`: function handling the addition of a suggestion.
- `Options` `IEnumberable<TItem>?`: all suggestions options/objects currently stored.
- `OptionText` `Func<TItem, string>`: function handling the display text for the suggestion.
- `OptionValue` `Func<TItem, object>`: function handling the display object/value for the suggestion.
- `SearchChanged` `EventCallback<string>`: function handling the change of user search input.
  - Every time there is a change in the input, whether it be the addition or subtraction of characters, this is invoked.
- `SelectedItem` `TItem?`: the item that has been selected by the user.
- `SelectedItemChanged` `EventCallback<TItem>`: function handling when the selected item has changed.

**Features**:
- Users have the ability to save new suggestions.
- Users have the ability to delete suggestions.
- Users have the ability to select suggestions from a drop-down list using either their mouse or their keyboard.
- Users have the ability to view all suggestions by clicking the component.

**Requirements**:
- Suggestions will appear as long as the saved suggestions contain the user's typed text.
- Suggestions specific to the user's typed text will appear after the user has typed at least one character.

---

### Bootstrap Tabs

This component represent `Tabs` in any consuming application.

**Parameters**:
- `Content` `RenderFragment`: the container for the content of the `Tab`.
- `Header` `RenderFragment`: the container for the `Tab` items.
- `HeaderExtraClasses` `string?`: the additional styling classes to be applied to the `Header`.
- `HeaderOnClick` `Action<MouseEventArgs>?`: an event to occur when the `Header` is clicked.
- `Parent` `BCBSTabGroup`: the group container for all tabs.

**Features**:
- Users are able to click on a `Tab` and view the content within it.
- Users are able to close tabs.
- Users are able to include additional tabs (if available) into the `TabGroup`.

---

### Changelog

This component was made to allow for greater communication between the developer and the end user by displaying the latest Git commit messages related to the consuming application.

- There are three categories:
  - *Bug Fixes*: `type = "fix"`
  - *New Features*:  `type = "feat"`
  - *Boring technical changes*

**Parameters**:
- `ChangelogError` `bool`: whether there was an issue with loading the `Modal`.
- `ChangelogLoaded` `bool`: whether the `Modal` has been loaded.
- `ChangelogOpen` `bool`: whether the `Modal` is currently activated/in view.
- `Modal` `Modal`: the container of the `Changelog`.

**Features**:
- Users can view the latest changes to a consuming application.

**Requirements**:
- The categorisation syntax required for the commit message: `type(scope): message`
  - The scope is optional and there are no categorisation restrictions for it. There are also obviously no categorisation restrictions for the message either.

---

### DataGrid

This component simulates an Excel grid with columns and rows.

**Parameters**:
- `ActiveSorting`: the active sorting policy for the table. If nothing is set, `DefaultSorting` is used.
  - This should be unique across the application.
- `CanUserReorderColumns:`: whether the user can reorder the columns in the table.
- `CanUserSortColumns`: whether the user can sort columns in the table.
- `DefaultSorting`: the default sorting policy for the table.
- `Items`: the content of the table.
- `ItemClick`: an event to occur when the user clicks on a row in the table.
- `ItemFilter`: the filter for the items.
- `ItemSize`: the size of the items in the table.
- `Name`: the name of the table.
- `OnColumnSort`: an event to occur when the user sorts the table.
- `OverscanCount`: the number of hidden rows loaded that aren't currently displayed within the table.
- `RowClass`: the styling class to apply to all `DataGridRow` in the table.
- `TableLayout`: the flexibility of the widths of all `DataGridColumn` in the table.
- `TableClass`: the styling class applied to the table.
  - To disable this, set this to `Int32.MaxValue`

**Features**:
- Users can export all `DataGrid` context to CSV.

**Developer Features**:
- Developers can set sorting/filtering policies of the `DataGrid` through the attached `ViewModel`.

**Requirements**:
- The `DataGrid` cannot render without a `ViewModel` attached. 
---
#### DataGridColumn
  **Parameters**:
  - `CanBeHidden` `bool`: whether the column can be hidden.
  - `CellClass` `Func<TRowData, string>?`: additional styling classes to be applied to cells in the column.
  - `CellClassStatic` `string?`: the styling class to be applied to cells in the column.
  - `CellClick` `Action<TRowData>?`: an event to occur when a cell is clicked in the column.
  - `CellClickStopPropagation` `bool`: whether the ``CellClick` events are enabled.
  - `CellDblClick` `EventCallback<TRowData>`: an event to occur when a cell is double clicked in the column.
  - `CellTitle` `Func<TRowData, string>?`: the value to display when the user hovers over the cell.
  - `ColumnHeading` `string`: the heading of the column.
  - `ColumnHeadingFull` `string?`: the full heading of the column (if the heading is too long and is shortened in `ColumnHeading`)
  - `ColumnOrder` `int`: the numbered order of the column.
  - `DefaultExportExpression` `Func<TRowData, string?>`: the default value of the column to be used when the data is exported.
  - `Editable` `Func<TRowData, bool>?`: whether the values in the column can be edited.
    - The change can be saved by pressing Enter, Tab, or switching focus elsewhere.
  - `ExportExpression` `Func<TRowData, string?>`: the value of the column to be used when the data is exported. This will switch between `DefaultExportExpression` and `CustomExportExpression` if set.
  - `FieldExpression` `Func<TRowData, object?>`: a function handling the display value of the column.
  - `Format` `string?`: the formatting expression to be used on the display value of the column.
  - `FormatForItem` `Func<TRowData, string>?`: the formatting expression to be used on specified display values of the column.
  - `InputStep` `double?`: incremental/decremental value for the input in the column.
  - `InputTitle` `string?`: the title mapped to the input.
  - `OnSave` `Action<TRowData, TSelectOption>`: an event to occur when an item is saved.
  - `Options` `IEnumerable<TSelectOption>`: the collection of options for the drop-down selection list in a column.
  - `OptionsForItem` `Func<TRowData, IEnumerable<TSelectOption>>`: the display names to use for the drop-down selection list options in a column.
  - `OptionText` `Func<TSelectOption, string>`: the display text for the options in the drop-down selection list in the column.
  - `OptionTextForItem` `Func<TSelectOption, TRowData>`: the display text for a specific option in the drop-down selection list in the column.
  - `OptionValue` `Func<TSelectOption, string>`: the values of the options in the drop-down selection list in a column.
  - `OptionValueForItem` `Func<TSelectOption, TRowData, string>?`: the value of a specific option in a drop-down selection list in a column.
  - `Parent` `IDataGrid<TRowData>`: the `DataGrid` the column belongs to.
  - `Pattern` `string?`: the REGEX pattern to use for input validation in the column.
  - `SortDirection` `DataGridColumnSortDirection`: the sort direction for the column (ascending, descending)
  - `SortExpression` `Func<TRowData, object?>`: a function handling the display value being used for the column's sorting.
  - `Type` `string`: the type of input the editable field in the column should be accounting for.
  - `Visible` `bool`: whether the column is currently visible.
  - `Width` `double?`: the width of the column.


  **Features**:
  - Users are able to rearrange/sort the columns with a drop-down menu.
  - Users are able to hide/show columns.

  **Developer Features**:
  - Developers can set column widths can be set to adjustable or fixed for the user.
  - Developers can set whether columns are sortable for the user.

---
#### DataGridRow
  **Developer Features**:
  - Developers can set dynamic styling for each row, dependent on its contents.
  - Developers can set how many rows are pre-loaded for virtualisation when not in the ViewPort.
  - Developers can set a custom event to occur when the user clicks on a row.

---
#### DataGridCell

  **User Features**
  - Users can edit information in cells.
  
  **Developer Features**:
  - Developers can set the height of items in the cells.
  - There are several `DataGridCell` implementations:
    - **AutoComplete**: uses the `AutoComplete` component.
    - **Check**: a component comprising of a check-box.
    - **Select**: a component comprising of a drop-down list of options.
    - **Static**: a component that displays an item.
    - **TextEdit**: a component that displays an item, with the ability to edit the item's value.
---

### FooterError

**Parameters**
- `FooterErrorExists` `bool`: whether there is currently an error that is being displayed by the component.
- `IsShortcutHandler` `bool?`: whether the `TradingToolConnector` is sending hotkeys to the tool.
- `Severity` `string?`: dictates the level of the error, `"warning"` or `"danger"`
- `TradingToolConnectorState` `TradingToolConnectorState`: the state of the tool's connection to the `TradingToolConnector`.
- `TradingToolConnectorUrl` `string`: the URL to download the `TradingToolConnector` from.

**User Features**
- Users are able to see if the tool is having connection issues with the server.
---

### MainLayout

This is just an skeleton container component for all other components.

---
###  NumberInput

There are Blazor limitations to saving an inputted decimal value straight away, therefore this component was created to ensure that does not happen.

---
### ServerSelector

**User Features**
- Users are able to choose and fail over to another server if possible.
