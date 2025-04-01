

# URL Parameter Structure

> **Status**: Accepted  
> **Date**: 16.03.2021  
> **Updated**: 17.11.2023  

---

## Context

The mapviewer application uses URL parameters to configure layers. The current format has limitations:

- **Confusing Syntax**: The order of parameters determines attribution, making it error-prone for configurations with multiple layers.  
- **Inefficient Representation**: Unnecessary characters are used to represent default values (e.g., `layers_opacity=0.85,,,,`).  

Example of the current format:
```text
layers=ch.swisstopo.pixelkarte-farbe-winter,ch.bafu.wrz-jagdbanngebiete_select
layers_opacity=0.85,0.6,,,
layers_visibility=true,false,true
```

To address these issues, a new, more compact and flexible format is proposed.

---

## Decision

### Generic Pattern for Layer Configuration

The new format simplifies layer configuration using a semicolon-separated structure:

```text
layers={layerID1},{visibility=t|f},{opacity=%f};{layerID2},{visibility=t|f},{opacity=%f}
```

### Examples

- One layer with default settings:  
  ```text
  layers={layerID1}
  ```
- Two layers with default settings:  
  ```text
  layers={layerID1};{layerID2}
  ```
- One layer with custom visibility and opacity:  
  ```text
  layers={layerID1},f,0.7
  ```

### Parametrized Layers

The `layerID` can include additional parameters (e.g., time):  
```text
ch.swisstopo.zeitreihen@time=18641231@height=100m
```

Timestamps must follow ISO8601 standards:  
- `YYYY-MM-DD`  
- `YYYY-MM-DDThh:mm:ss`  

### External Layers

External layers use a consistent format:  
- WMS: `WMS|GET_CAP_BASE_URL|LAYER_ID`  
- KML: `KML|URL|TITLE`  
- GPX: `GPX|URL|TITLE`  

---

## Consequences

### Positive Consequences

- **Improved Readability**: The new format is more compact and easier to understand.  
- **Flexibility**: Supports optional fields and parametrized layers for future extensibility.  

### Negative Consequences

1. **Increased Parsing Complexity**: The new format introduces optional fields and parametrized values, making parsing and validation more complex.  
2. **Legacy Support Burden**: Maintaining retro-compatibility with the old format adds development and maintenance overhead.  

---

## Implementation Notes

- The new viewer must implement a **retro-compatibility module** to interpret the old format and convert it to the new one.  
- Errors in the URL parameter format should be logged to the console for debugging purposes.  
