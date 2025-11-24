# Technical Note: SwiftUI Transition Removal Bug

## The Issue
When conditionally removing a view from a `ZStack` (e.g., `if isVisible { MyView() }`), standard transitions like `.move` or `.opacity` often fail. The view disappears instantly ("pops" out) instead of animating out.

## The Root Cause
SwiftUI manages transitions by creating a temporary "ghost" view during the removal frame. In complex hierarchies (especially `ZStack`s), if the z-ordering is implicit, the layout engine may lose track of the ghost view's layer context or render it behind other opaque views immediately upon state change.

## The Solution
Apply an explicit, stable `.zIndex` to the view being removed.

```swift
if isVisible {
    SlidingPanel()
        .transition(.move(edge: .top))
        .zIndex(100) // Force stable layering
}
```

## Why It Works
1.  **Layer Identity:** The explicit `.zIndex` tells SwiftUI this view occupies a specific rendering plane.
2.  **Persistence:** During the removal transition, even though the view is removed from the logical hierarchy, the rendering engine respects the z-index for the visual "ghost" view, ensuring it remains on top of siblings and visible for the duration of the animation.
