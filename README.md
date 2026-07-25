<h1>
    JewHack Addon Documentation
</h1>

A quick reference for building addons with the JHK UI library.

> Icons: browse available icons at [jewhack.org/icons](https://jewhack.org/icons)
> Tip: use `Flag`s + `JHK:GetFlag()` wherever possible — they make reading/writing values from other parts of your script much easier.

## Setup

All addon code must wait for `JHK` (and its `Addons` table) to exist before running:

```lua
repeat
    task.wait()
until getgenv().JHK and getgenv().JHK.Addons
```

> **Note:** Addons are rendered inside a single tab. All sections you create will live inside that one tab — you can't spread sections across multiple tabs.

## Sections

Sections are the containers you add UI elements to.

```lua
-- Name, Side ("left" or "right")
local Section = JHK:AddSection("Addon Section", "left")
```

## Elements

### Toggle (+ optional Keybind)

Toggles need a label. Keybinds sync to a toggle's state automatically via `SyncToggle`.

```lua
Section:AddLabel("Toggle Example"):AddToggle({
    Default = false,
    Flag = "myToggle",
    Callback = function(Value)
        JHK:Notify("Addon", "Toggle set to " .. tostring(Value), 5, "small", "bell")
    end
}):AddKeybind({
    Default = "None",   -- Keybinds do NOT appear on mobile, computer only
    Text = "Keybind",
    SyncToggle = true,  -- All JHK keybinds sync to a toggle
    Mode = "Toggle",
    Flag = "myKeybind"
})
```

### Slider

```lua
Section:AddSlider({
    Text = "Slider",
    Default = 10,
    Min = 1,
    Max = 50,
    Rounding = 0,
    Flag = "mySlider",
    Callback = function(Value)
        JHK:Notify("Addon", "Slider set to " .. tostring(Value), 2, "small", "bell")
    end
})
```

### Dropdown (single-select)

```lua
Section:AddDropdown({
    Text = "Single Dropdown",
    Default = "1",
    Values = {"1", "2", "3"},
    Flag = "myDropdown",
    Callback = function(Value)
        JHK:Notify("Addon", "Selected: " .. Value, 5, "small", "bell")
    end
})
```

### Dropdown (multi-select)

```lua
Section:AddMultiDropdown({
    Text = "Multi Dropdown",
    Default = {"1"},
    Values = {"1", "2", "3"},
    Flag = "myMultiDropdown",
    Callback = function(Values)
        JHK:Notify("Addon", "Selected: " .. table.concat(Values, ", "), 5, "small", "bell")
    end
})
```

### Color Picker

```lua
Section:AddColorPicker({
    Text = "Favorite Color",
    Default = Color3.fromRGB(255, 0, 0),
    Flag = "myColor",
    Callback = function(Value)
        print("Color changed to:", Value)
    end
})
```

### Buttons

```lua
Section:AddButton({
    Name = "Send Notifications",
    Callback = function()
        -- title, content, duration (seconds), size ("big"/"small"), icon (small only)
        JHK:Notify("Notification", "This is a large notification.", 5, "big")
        JHK:Notify("Notification", "This is a small notification.", 5, "small", "bell")
    end
})
```

**Reading flag values with `GetFlag`:**

```lua
Section:AddButton({
    Name = "Print Flags",
    Callback = function()
        JHK:Notify("Notification", "Toggle: " .. tostring(JHK:GetFlag("myToggle")), 5, "small", "bell")
        JHK:Notify("Notification", "Dropdown: " .. tostring(JHK:GetFlag("myDropdown")), 5, "small", "bell")
        JHK:Notify("Notification", "Color: " .. tostring(JHK:GetFlag("myColor")), 5, "small", "bell")
    end
})
```

**Writing flag values with `SetValue`:**

```lua
Section:AddButton({
    Name = "Toggle It",
    Callback = function()
        local current = JHK:GetFlag("myToggle")
        JHK.Flags.myToggle:SetValue(not current)
    end
})
```

**Checking executor capabilities with `CanUse`:**

```lua
Section:AddButton({
    Name = "Check Environment",
    Callback = function()
        local functionsToCheck = {
            "getrawmetatable",
            "hookfunction",
            "getconnections",
            "setreadonly",
            "firetouchinterest",
            "setrenderproperty",
            "require"
        }

        for _, fn in functionsToCheck do
            local supported = JHK:CanUse(fn) and "supported" or "unsupported"
            JHK:Notify("Notification", fn .. " is " .. supported, 4, "small", "bell")
            task.wait()
        end
    end
})
```

## Notifications

```lua
JHK:Notify(
    "Notification", -- Title
    "Welcome to JewHack!", -- Message
    5,               -- Duration (seconds)
    "small",         -- Size: "big" or "small"
    "bell"           -- Icon (only used for "small")
)
```

## Reference: Notify Parameters

| Param | Type | Notes |
|---|---|---|
| Title | string | Notification title |
| Content | string | Main message body |
| Duration | number | Seconds shown |
| Size | string | `"big"` or `"small"` |
| Icon | string | Only applies when `Size` is `"small"` — see [jewhack.org/icons](https://jewhack.org/icons) |
