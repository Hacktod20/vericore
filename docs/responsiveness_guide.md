# Windows Application Responsiveness Guide

Creating a responsive Windows desktop application ensures a consistent and optimal user experience across a wide range of devices, from older laptops with low-resolution displays to modern high-DPI 4K monitors.

## Standard Screen Sizes and Resolutions

When designing for Windows, you should account for the following common screen resolutions:

- **1366 x 768 (HD)**: The most common baseline resolution, particularly for budget laptops and older displays. **UI must function perfectly here without scrolling if possible.**
- **1920 x 1080 (Full HD / 1080p)**: The standard for most modern desktops and mid-range laptops.
- **2560 x 1440 (QHD / 1440p)**: Becoming standard for high-end desktop monitors.
- **3840 x 2160 (4K UHD)**: High-end displays. Often used with display scaling (e.g., 150% or 200%).

## Display Scaling (DPI)

Windows natively scales UI elements on high-resolution displays to ensure they remain legible. Your Electron app inherits this scaling.
- A 4K monitor set to 200% scaling effectively renders your app as if it were on a 1920x1080 screen from a CSS pixel perspective, but with sharper assets.
- **Best Practice:** Avoid raster images (PNG/JPG) for UI icons. Use SVG or icon fonts to ensure they remain crisp regardless of the scaling factor.

## Responsive Design Strategies for Electron

### 1. Liquid Layouts (Flexbox and Grid)
Avoid fixed widths (`width: 800px`) and heights for your main containers.
- **CSS Grid:** Ideal for the main layout shell (Sidebar + Header + Content Area). It allows you to lock the sidebar width while letting the content area stretch (`grid-template-columns: 250px 1fr`).
- **Flexbox:** Ideal for aligning components within cards or rows (e.g., keeping a badge aligned to the right of a hardware name).

### 2. Handling Congestion and Text Overflow
When building cards with detailed hardware metrics:
- Use `text-overflow: ellipsis; white-space: nowrap; overflow: hidden;` on long text fields (like CPU model names or UUIDs) to prevent them from breaking the layout or wrapping uncomfortably on small screens.
- Implement tooltips (via `title` attribute or a custom tooltip component) so users can hover to see the full text if it's truncated.

### 3. Media Queries
Use media queries to adjust the layout when the window size drops below critical breakpoints.
```css
/* Standard Breakpoint for adjusting dense UI elements */
@media screen and (max-width: 1200px) {
  .dashboard-grid {
    grid-template-columns: repeat(2, 1fr); /* Drop from 3 columns to 2 */
  }
}

@media screen and (max-width: 900px) {
  .dashboard-grid {
    grid-template-columns: 1fr; /* Stack everything vertically on very small screens */
  }
}
```

### 4. Minimum Window Size
In your `main.js` Electron configuration, enforce a `minWidth` and `minHeight` to prevent the app from being resized into a state where it's completely unusable.
```javascript
const mainWindow = new BrowserWindow({
  width: 1280,
  height: 800,
  minWidth: 1100, // Prevents layout from completely breaking
  minHeight: 700,
});
```

By adhering to these principles, the VeriCore dashboard will seamlessly adapt whether the user is on a tiny legacy laptop screen or a modern high-end workstation.
