# Form Resizer for C#

## Overview

This C# utility simplifies the dynamic resizing of controls on a Windows Form. It's designed to effortlessly adapt controls to different form sizes while maintaining proportions and font scaling.

## Features

- **Automatic Resizing**: Controls and fonts adjust based on form dimensions.
  
- **Panel Support**: Ensures controls within panels are automatically adjusted.

- **Aspect Ratio Maintenance**: Keeps the original aspect ratio of controls for a visually consistent interface.

## Usage

1. **Include in Your Project:**
   - Add the `ControlLayoutManager.cs` file to your C# project.

2. **Load Controls:**
   - Call `LoadControls();` in the `Form_Load` event to capture the original state of controls.

3. **Implement Resizing Logic:**
   - Call `ResizeChildrenControls();` in the `Form_SizeChanged` event to trigger resizing when the form size changes.

4. **Customization:**
   - Customize font scaling and other parameters as needed.

## Example

```csharp
private Rectangle originalFormRectangle;
private Dictionary<Control, Rectangle> controlRectangles = new Dictionary<Control, Rectangle>();
private Dictionary<Control, float> controlFontSizes = new Dictionary<Control, float>();
private float fontScale = 1f;

private void LoadControls()
{
    originalFormRectangle = new Rectangle(this.Location, this.Size);

    foreach (Control control in this.Controls)
    {
        AddControlAndFont(control);

        // Check if the control is a panel
        if (control is Panel panel)
        {
            // Iterate through controls inside the panel
            foreach (Control panelControl in panel.Controls)
            {
                AddControlAndFont(panelControl);
            }
        }
    }
}

private void AddControlAndFont(Control control)
{
    controlRectangles.Add(control, new Rectangle(control.Location, control.Size));
    controlFontSizes.Add(control, control.Font.Size);
}

private void ResizeChildrenControls()
{
    foreach (var controlRectangle in controlRectangles)
    {
        ResizeControl(controlRectangle.Key, controlRectangle.Value, controlFontSizes[controlRectangle.Key]);
    }

    sizeNewFormsPanel = panelForms.Size;
}

private void ResizeControl(Control control, Rectangle originalControlRectangle, float originalFontSize)
{
    float xRatio = (float)this.ClientRectangle.Width / (float)originalFormRectangle.Width;
    float yRatio = (float)this.ClientRectangle.Height / (float)originalFormRectangle.Height;

    float newX = originalControlRectangle.Location.X * xRatio;
    float newY = originalControlRectangle.Location.Y * yRatio;

    control.Location = new Point((int)newX, (int)newY);
    control.Width = (int)(originalControlRectangle.Width * xRatio);
    control.Height = (int)(originalControlRectangle.Height * yRatio);

    float ratio = xRatio;
    if (xRatio >= yRatio)
    {
        ratio = yRatio;
    }

    float newFontSize = originalFontSize * ratio * fontScale;
    try
    {
        Font newFont = new Font(control.Font.FontFamily, newFontSize, control.Font.Style);
        control.Font = newFont;
    }
    catch { }
}
```

## License

This project is licensed under the [MIT License](LICENSE) - see the [LICENSE](LICENSE) file for details.

---
