
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

**Features**:
- Users have the ability to save new suggestions.
- Users have the ability to delete suggestions.
- Users have the ability to select suggestions from a drop-down list using either their mouse or their keyboard.
- Users have the ability to view all suggestions by clicking the component.

**Requirements**:
- Suggestions will appear as long as the saved suggestions contain or start with the user's typed text.
- Suggestions specific to the user's typed text will appear after the user has typed at least one character.

---

### Bootstrap Tabs

This component represent Tabs in any consuming application.

**Features**:
- Users are able to click on a Tab and view the content within it.
- Users are able to close tabs.

---

### Changelog

This component was made to allow for greater communication between the developer and the end user by displaying the latest Git commit messages related to the consuming application.

**Features**:
- Users can view the latest changes to a consuming application.

---

### DataGrid

This component simulates an Excel grid with columns and rows.

**Features**:
- Users are able to rearrange the columns with a drop-down menu.
- Users are able to hide/show columns.

**Developer Features**:
- Developers can set column widths can be set to adjustable or fixed for the user.

**Requirements**:
- The DataGrid cannot render without a ViewModel attached.
