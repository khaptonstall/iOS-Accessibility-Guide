🚧 **This page is a work in progress** 🚧

# iOS Accessibility Guide
The purpose of this guide is to help you understand the fundamentals of accessibility APIs on iOS and give you the tools to build not just bare-minimum accessible apps, but enjoyable mobile experiences for those using Assistive Technologies.

While this guide primarily focuses on and uses examples of how a VoiceOver user will perceive a user interface, building your interface with VoiceOver users in mind will have considerable overlap with users of other Assistive Technologies thanks to the way iOS uses the core accessibility APIs.

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

Value correlates to an element's `accessibilityValue` property. While many elements won't have a value, there are some clear examples which do, such as a `UISlider`:

```swift
let slider = UISlider()
slider.minimumValue = 0
slider.maximumValue = 10
slider.value = 2
// slider.accessibilityValue == "20%"
```

## How iOS Communicates Name, Role, and Value to the User
Understanding how the system communicates name, role, and value to the user helps you as a developer write your code with empathy for those who rely on Assistive Technologies to navigate the products you're putting into the world.

### Examples of Familiar UIKit Components
Let's start with how the system communicates some of the most familiar iOS components.

`UILabel`:
```swift
let label = UILabel()
label.text = "Hello World!
```
The user hears: "Hello World!":
- "Hello World!" represents the **name**
- The component has no **role** or **value** as its just static text

`UIButton`:
```swift
let button = UIButton()
button.setTitle("Download", for: .normal)
```
The user hears: "Download, Button":
- "Download" represents the **name**
- "Button" represents the **role**, letting the user know this element behaves like a button (e.x. accepts tap events)
- The component has no **value**

`UISlider`:
```swift
let slider = UISlider()
slider.minimumValue = 0
slider.maximumValue = 10
slider.value = 5
```
The user hears "50%, Adjustable":
- "50%" represents the **value**
- "Adjustable" represents the **role**, letting the user know this component allows for continuous adjustment through a range of values
- The component has no **name**

### Examples Where the System Needs Your Help
As we saw in the last example with the `UISlider`, the component had no **name**. How does the user know what they're changing the value of when they interact with the slider? This is where you come in. Let's look at some situations where you'll need to assist iOS in making components fully accessible.

#### System Components Without Text/Title Properties

It's clear to us after seeing how a `UISlider` is read aloud to a user that we need to do a bit more work to ensure the component has a clear **name** to inform the user what exactly they're adjusting. Remember that the **name** is represented by the component's `accessibilityLabel`, so we only need one line of code:
```swift
let slider = UISlider()
slider.minimumValue = 0
slider.maximumValue = 10
slider.value = 5
slider.accessibilityLabel = "Volume"
```
The user now hears "Volume, 50%, Adjustable", which correspond to the **name**, **value**, and **role**, respectively.

#### System Components Represented With Images

Another common example where we'll need to manually add a name is when we have a `UIButton` that uses an image rather than a title to represent what it does. Let's say we have a download button using an icon from our Assets catalog:
```swift
let button = UIButton()
button.setImage(UIImage(named: "square-and-arrow-down-filled"), for: .normal)
```
The user hears "square and arrow down filled, Button":
- "square and arrow down filled" represents the **name**, which the system automatically derived from the name of the image
- "Button" represents the **role**

You may luck out if your image was named "Download", but its much better practice to manually set the **name** when using images on controls:
```swift
let button = UIButton()
button.setImage(UIImage(named: "square-and-arrow-down-filled"), for: .normal)
button.accessibilityLabel = "Download"
```
The user now hears "Download, Button".

#### Custom UI Components
