# Super Field - Options

A versatile multi-select options component for Budibase applications with multiple control types, data sources, and display modes.

## 🚀 Features

### Selection Types

- **Multiple Control Types**: Select dropdown, input select, checkboxes, switches, ordered list
- **Single/Multi Selection**: Support for both single and multiple option selection
- **Array Field Binding**: Dedicated array field for selected values
- **Default Values**: Pre-selected options
- **Validation**: Comprehensive selection validation

### Data Sources

- **Schema Options**: Options from data schema
- **Data Queries**: Dynamic options from data sources with filtering and sorting
- **Custom Options**: Manually defined option lists
- **Real-time Updates**: Dynamic option loading and updates
- **Large Datasets**: Efficient handling with pagination and limits

### Display Modes

- **Visual Options**: Text with colors, pill-style, or plain text display
- **Layout Control**: Column or row arrangement for options
- **Icon Support**: Visual indicators for options
- **Color Coding**: Color-coded options for better UX
- **Ordered Lists**: Drag-and-drop reordering for priorities

### User Experience

- **Intuitive Controls**: Familiar selection interfaces
- **Search & Filter**: Quick option finding in large lists
- **Event Handling**: On change events with selection context
- **Auto-focus**: Automatic focus for data entry
- **Debounced Input**: Performance optimization for large option sets

### Advanced Features

- **Toggle All**: Select/deselect all options (switch mode)
- **Reorder Only**: Pure ordering without selection changes
- **Conditional Logic**: Dynamic options based on other fields
- **Button Integration**: Custom action buttons for option management
- **Accessibility**: Full keyboard navigation and screen reader support

### Styling & Layout

- **Flexible Positioning**: Label placement options
- **Field Modes**: Form input or inline editing
- **Size Configuration**: Adjustable component width
- **Theme Integration**: Consistent with Budibase design

## 📝 Usage Instructions

### Basic Setup

1. Add the Super Field - Options component to your form
2. Choose options source (schema, data, or custom)
3. Select control type (select, checkboxes, etc.)
4. Configure option display and validation

### Advanced Configuration

- **Data Binding**: Connect to data sources with filtering
- **Display Modes**: Choose appropriate visual style
- **Validation**: Set selection requirements and limits
- **Events**: Attach actions to selection changes

### Common Use Cases

- **Category Selection**: Product categories or tags
- **Permission Settings**: User role and access selections
- **Survey Questions**: Multiple choice with multiple answers
- **Feature Selection**: Product features or add-ons
- **Priority Ranking**: Ordered preference lists

## 🔧 Configuration Options

| Setting           | Type    | Description                |
| ----------------- | ------- | -------------------------- |
| Field             | Array   | Options field binding      |
| Label             | String  | Display label text         |
| Placeholder       | String  | Selection guidance text    |
| Default Value     | Array   | Pre-selected options       |
| Help Text         | String  | Help/instruction text      |
| Validation        | Rules   | Selection validation rules |
| Options Source    | Select  | Schema/Data/Custom options |
| Control Type      | Select  | Selection interface type   |
| Direction         | Select  | Column/Row layout          |
| Autofocus         | Boolean | Auto-focus on load         |
| Debounced         | Boolean | Enable input debouncing    |
| Disabled          | Boolean | Disable option selection   |
| Read Only         | Boolean | Read-only mode             |
| Toggle All        | Boolean | Show select all toggle     |
| Reorder Only      | Boolean | Pure reordering mode       |
| Icon              | Icon    | Visual indicator icon      |
| Field Mode        | Select  | Form or inline input style |
| Label Position    | Select  | Label placement            |
| Size              | Number  | Component width span       |
| Options View Mode | Select  | Text/Color/Pills display   |

## 📋 Events

### On Change

Triggered when options are selected or deselected.

**Context:**

- `value`: Array of selected option values
- `field`: The bound field information

## 🎨 Styling

The component supports advanced styling options:

- **Option Display**: Multiple visual styles for options
- **Layout Control**: Flexible arrangement options
- **Color Integration**: Theme-consistent color usage
- **Responsive**: Mobile-optimized selection interfaces

## 🔍 Best Practices

- Choose appropriate control type for use case
- Use data sources for dynamic option lists
- Consider mobile users for touch interfaces
- Provide clear option labels and descriptions
- Implement validation for required selections
- Test with various option counts and lengths
