# LuaCSS-Roblox-Reactive-UI-Framework
# LuaCSS UI Framework Documentation

LuaCSS is a powerful UI framework for Roblox that provides a CSS-like approach to creating and styling GUI elements with reactive state management, animations, and component-based architecture.

## Table of Contents

1. [Getting Started](#getting-started)
2. [Core Concepts](#core-concepts)
3. [API Reference](#api-reference)
4. [Component System](#component-system)
5. [Reactive Values](#reactive-values)
6. [Styling](#styling)
7. [Animations](#animations)
8. [Examples](#examples)

## Getting Started

### Basic Usage

```lua
local luacss = require(path.to.luacss)

-- Create a basic frame
local frame = luacss.compileObject({
    class = "Frame",
    name = "MyFrame",
    size = {0.5, 0, 0.5, 0},
    position = {0.25, 0, 0.25, 0},
    groundcolor = Color3.fromRGB(100, 150, 200),
    rounded = {0, 10}
})
```

### Project Structure
- Main module: Contains the core LuaCSS functionality
- Functions module: Contains all the custom styling methods
- Libraries: SPR (animations), Janitor (cleanup), etc.

## Core Concepts

### Object Compilation
Objects are created using table-based configurations that describe both the Roblox instance type and its properties.

```lua
local button = luacss.compileObject({
    class = "TextButton", -- Roblox instance type
    name = "MyButton",
    text = "Click Me",
    size = {0, 200, 0, 50},
    groundcolor = "#3498db",
    txtcolor = Color3.new(1, 1, 1),
    clicked = function(obj)
        print("Button clicked!")
    end
})
```

### Property Processing
Properties can be:
- **Direct values**: Applied directly to the instance
- **Custom functions**: Processed through the methods system
- **Environment values**: Reactive values that update automatically
- **Component references**: Pre-defined style configurations

## API Reference

### Core Functions

#### `luacss.compileObject(properties, logs?)`
Creates a new GUI object from a property table.

**Parameters:**
- `properties` (table): Object configuration
- `logs` (boolean, optional): Enable debug logging

**Returns:**
- `Instance`: The created GUI object
- `VirtualGuiObject`: Enhanced object with additional methods
- `table`: Original properties

#### `luacss.edit(object, properties)`
Applies properties to an existing GUI object.

**Parameters:**
- `object` (Instance): Target GUI object
- `properties` (table): Properties to apply

#### `luacss.style(name, properties)`
Register a reusable style.

```lua
luacss.style("button-primary", {
    groundcolor = "#3498db",
    txtcolor = Color3.new(1, 1, 1),
    rounded = {0, 5}
})

-- Usage
local button = luacss.compileObject({
    class = "TextButton",
    style = "button-primary"
})
```

#### `luacss.component(name, data)`
Register a reusable component.

```lua
local myComponent = luacss.component("Card", {
    class = "Frame",
    groundcolor = Color3.new(1, 1, 1),
    rounded = {0, 10},
    shadow = {Scale = 1.2, Color = Color3.new(0, 0, 0)}
})
```

### Utility Functions

#### `luacss.scope()`
Create a scoped context for automatic cleanup.

```lua
local scope = luacss.scope()
local frame = scope.compileObject({...})
-- Cleanup all objects when done
scope:Cleanup()
```

#### `luacss:janitor()`
Get a janitor instance for manual cleanup management.

#### `luacss.spring(object, properties)`
Animate object properties with spring physics.

#### `luacss.value(initial)`
Create a reactive value.

```lua
local count = luacss.value(0)
count:set(5) -- Update value
print(count:get()) -- Get current value
```

## Component System

### Creating Components

Components are reusable UI configurations:

```lua
-- From table
local cardComponent = luacss.component("Card", {
    class = "Frame",
    groundcolor = Color3.new(0.95, 0.95, 0.95),
    rounded = {0, 8},
    shadow = {Scale = 1.1}
})

-- From existing instance
local existingFrame = -- ... some frame
local frameComponent = luacss.component("MyFrame", existingFrame)
```

### Using Components

```lua
-- Direct usage
local card = luacss.compileObject({
    style = "Card"
})

-- With overrides
local customCard = luacss.compileObject({
    style = {"Card", {groundcolor = Color3.fromRGB(255, 0, 0)}}
})

-- Multiple styles
local styledCard = luacss.compileObject({
    multistyle = {"Card", "shadow-large", "rounded-lg"}
})
```

### Component Methods

```lua
local component = luacss.component("MyComponent", {...})

component.makeGlobal() -- Make available globally
component.Destroy() -- Remove component
component.Reload() -- Reload from ModuleScript
component.Update(newData) -- Update component data
component.Inspect() -- Debug component
```

## Reactive Values

### Basic Usage

```lua
local backgroundColor = luacss.addEnvValue("bgColor", Color3.fromRGB(100, 100, 100))

-- Use in objects
local frame = luacss.compileObject({
    class = "Frame",
    groundcolor = "bgColor" -- References the env value
})

-- Update the value (automatically updates all references)
backgroundColor.Set(Color3.fromRGB(200, 100, 100))
```

### Value Operations

```lua
local count = luacss.value(0)

-- Subscribe to changes
local unsubscribe = count:subscribe(function(newValue)
    print("Count changed to:", newValue)
end)

-- Transform values
local doubled = count:map(function(x) return x * 2 end)
local evenOnly = count:filter(function(x) return x % 2 == 0 end)

-- Computed values
local computed = count:computed(function()
    return count:get() * 10
end)

-- Bind to GUI property
local bindSetter = count:bind(function(value)
    someFrame.Size = UDim2.fromScale(value, value)
end)
```

## Styling

### Layout Properties

```lua
{
    -- Size and Position
    size = {0.5, 0, 0.5, 0}, -- UDim2 as table
    position = {0, 100, 0, 50},
    anchor = {0.5, 0.5}, -- AnchorPoint
    
    -- Alignment shortcuts
    alignment = "center", -- "top", "bottom", "left", "right", "center"
    center = true, -- Quick center alignment
    
    -- Layout
    list = {"center", "center", {0, 10}, Enum.FillDirection.Vertical},
    grid = {"center", "center", {5, 5}, {100, 100}},
    
    -- Flexbox-like
    flexrow = 10, -- Horizontal layout with gap
    flexcolumn = 5, -- Vertical layout with gap
}
```

### Visual Properties

```lua
{
    -- Colors
    groundcolor = Color3.fromRGB(100, 150, 200), -- Background
    groundcolor = "#3498db", -- Hex colors
    groundcolor = "blue", -- Named colors
    txtcolor = Color3.new(1, 1, 1), -- Text color
    
    -- Transparency
    groundtransparency = 0.5,
    opacity = 0.8, -- Sets multiple transparency properties
    
    -- Borders and Corners
    rounded = {0, 10}, -- Corner radius
    border = true, -- Add border
    borderwidth = 2,
    
    -- Effects
    shadow = {Scale = 1.2, Color = Color3.new(0, 0, 0)},
    gradient = {
        Color = ColorSequence.new(Color3.new(1,0,0), Color3.new(0,0,1)),
        Rotation = 45
    }
}
```

### Padding and Spacing

```lua
{
    padding = {0, 10}, -- All sides
    paddingsides = {0, 15}, -- Left and right
    paddingvertical = {0, 10}, -- Top and bottom
    paddingtop = {0, 5},
    paddingbottom = {0, 5},
    paddingleft = {0, 10},
    paddingright = {0, 10}
}
```

### Text Properties

```lua
{
    text = "Hello World",
    txtsize = 16,
    font = Enum.Font.SourceSans,
    textAlignment = "center", -- "left", "right"
    textVerticalAlignment = "center", -- "top", "bottom"
    textStroke = {Thickness = 1, Color = Color3.new(0, 0, 0)}
}
```

## Animations

### Spring Animations

```lua
-- Using the target method
{
    target = {2, 5, {Position = UDim2.fromScale(0.5, 0.5)}} -- damping, frequency, properties
}

-- Using animate method
{
    animate = {
        Property = "Size",
        Value = UDim2.fromScale(1, 1),
        Damping = 2,
        Frequency = 5
    }
}

-- Position shortcuts
{
    animate = {
        Value = "center", -- Animates to center position
        Damping = 1,
        Frequency = 3
    }
}
```

### Hover Animations

```lua
{
    hovercolor = {
        Value = Color3.fromRGB(200, 100, 100),
        Animated = true,
        Damping = 2,
        Frequency = 5
    },
    leavecolor = {
        Value = Color3.fromRGB(100, 100, 100),
        Animated = true
    }
}
```

### Transition Effects

```lua
{
    fadeIn = {Duration = 0.5, Delay = 0.1},
    fadeOut = {Duration = 0.3},
    slideIn = {Direction = "left", Duration = 0.4}
}
```

## Interactive Elements

### Event Handlers

```lua
{
    clicked = function(obj)
        print("Clicked!")
    end,
    
    hovered = function(obj)
        -- Mouse enter
    end,
    
    left = function(obj)
        -- Mouse leave
    end,
    
    hover = {
        Enter = function(obj) end,
        Leave = function(obj) end
    }
}
```

### State Management

```lua
{
    states = {
        normal = function(obj)
            obj.BackgroundColor3 = Color3.new(0.5, 0.5, 0.5)
        end,
        
        active = function(obj)
            obj.BackgroundColor3 = Color3.new(0, 1, 0)
        end
    },
    
    state = "normal" -- Initial state
}

-- Change state later
luacss.state(myObject, "active")
```

## Advanced Features

### Child Elements

```lua
{
    class = "Frame",
    spawn = {
        Header = {
            class = "TextLabel",
            text = "Title",
            size = {1, 0, 0, 30}
        },
        
        Content = {
            class = "ScrollingFrame",
            size = {1, 0, 1, -30},
            position = {0, 0, 0, 30}
        }
    }
}
```

### Conditional Properties

```lua
{
    run = function(obj)
        if someCondition then
            obj.BackgroundColor3 = Color3.new(1, 0, 0)
        end
    end
}
```

### Device-Specific Styling

```lua
{
    fitToDevice = {
        Scale = 1,
        AspectRatio = 16/9,
        UseAspect = true
    }
}
```

## Examples

### Simple Button

```lua
local button = luacss.compileObject({
    class = "TextButton",
    name = "ActionButton",
    text = "Click Me!",
    size = {0, 200, 0, 50},
    alignment = "center",
    groundcolor = "#3498db",
    txtcolor = Color3.new(1, 1, 1),
    rounded = {0, 8},
    
    clicked = function(obj)
        print("Button clicked!")
    end,
    
    hovercolor = {
        Value = Color3.fromRGB(52, 152, 219),
        Animated = true
    },
    
    leavecolor = {
        Value = Color3.fromRGB(41, 128, 185),
        Animated = true
    }
})
```

### Card Component

```lua
local card = luacss.compileObject({
    class = "Frame",
    size = {0, 300, 0, 200},
    groundcolor = Color3.new(1, 1, 1),
    rounded = {0, 12},
    shadow = {Scale = 1.1, Color = Color3.fromRGB(0, 0, 0)},
    
    spawn = {
        Title = {
            class = "TextLabel",
            text = "Card Title",
            size = {1, -20, 0, 30},
            position = {0, 10, 0, 10},
            txtsize = 18,
            font = Enum.Font.SourceSansBold,
            groundtransparency = 1
        },
        
        Content = {
            class = "TextLabel",
            text = "Card content goes here...",
            size = {1, -20, 1, -50},
            position = {0, 10, 0, 40},
            txtsize = 14,
            groundtransparency = 1,
            textAlignment = "left",
            textVerticalAlignment = "top"
        }
    }
})
```

### Responsive Layout

```lua
local container = luacss.compileObject({
    class = "Frame",
    size = {1, 0, 1, 0},
    groundtransparency = 1,
    flexcolumn = 20,
    padding = {0, 20},
    
    spawn = {
        Header = {
            class = "Frame",
            size = {1, 0, 0, 60},
            groundcolor = "#2c3e50",
            flex = 0 -- Don't grow
        },
        
        Content = {
            class = "ScrollingFrame",
            groundcolor = "#ecf0f1",
            flex = 1, -- Grow to fill space
            CanvasSize = UDim2.new(0, 0, 2, 0)
        },
        
        Footer = {
            class = "Frame",
            size = {1, 0, 0, 40},
            groundcolor = "#34495e",
            flex = 0 -- Don't grow
        }
    }
})
```

## Best Practices

1. **Use Components**: Create reusable components for common UI patterns
2. **Leverage Reactive Values**: Use environment values for theming and dynamic content
3. **Scope Management**: Use scopes or janitors for proper cleanup
4. **Performance**: Avoid creating too many reactive connections
5. **Naming**: Use consistent naming conventions for styles and components
6. **Organization**: Group related styles and components together

## Performance Tips

- Use `luacss.scope()` for automatic cleanup
- Minimize reactive value usage in frequently updated elements
- Batch style updates when possible
- Use component system for repeated patterns
- Consider using `runinsert` for heavy computations

This documentation covers the core functionality of the LuaCSS framework. The system is highly extensible and provides a modern approach to UI development in Roblox with familiar CSS-like concepts.
