🚧 **This page is a work in progress** 🚧

# iOS Accessibility Guide
The purpose of this guide is to help you understand the fundamentals of accessibility APIs on iOS and give you the tools to build not just bare-minimum accessible apps, but enjoyable mobile experiences for those using Assistive Technologies. 

# Accessibility Basics
Before diving into the code, it's important to understand some of the basic key terms when it comes to user interface accessibility, as defined by the [Web Content Accessibility Guidelines (WCAG)](https://www.w3.org/WAI/standards-guidelines/wcag/).

## Name, Role, Value
These 3 key terms help enable Assistive Technologies (such as VoiceOver) express user interface elements so that any user can understand and interact with your app.

### Name
A name is used to inform the user of an element's purpose.

Name correlates to an element's `accessibilityLabel` property. For some common elements, this value is automatically derived by the system:

```swift
let label = UILabel()
label.text = "Accessibility Basics"
// label.accessibilityLabel == "Accessibility Basics"

let button = UIButton()
button.setTitle("Share", for: .normal)
// button.accessibilityLabel == "Share"
```

But for elements without a clear textual component (or for custom UI elements), you must ensure you set the `accessibilityLabel` yourself:

```swift
let slider = UISlider()
slider.minimumValue = 0
slider.maximumValue = 11
slider.accessibilityLabel = "Volume"
```

### Role
The role expresses the affordances an element offers to the user. 

Role correlates to an element's `accessibilityTraits` property (which has the type `UIAccessibilityTraits`). These traits also help define an element's state (e.x. whether it is enabled or disabled) and properties (e.x. how Assistive Technologies may interact with the element).

```swift
let button = UIButton()
button.isEnable = false
// button.accessibilityTraits = [.button, .notEnabled]
```

### Value
An element's value can contain secondary information about that element and may change programmatically or with user interaction.

Value correlates to an element's `accessibilityValue` property. While many element's won't have a value, there are some clear examples, such as a `UISlider`:

```swift
let slider = UISlider()
slider.minimumValue = 0
slider.maximumValue = 10
slider.value = 2
// slider.accessibilityValue == "20%"
```
