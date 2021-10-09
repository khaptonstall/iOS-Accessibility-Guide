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

# Building Accessible User Interfaces
As we saw in the last example with the `UISlider`, the component had no **name**. How does the user know what they're changing the value of when they adjust slider? While UIKit components will often be accessible in the sense that a user can interact with them, it may not result in an enjoyable experience for the user and could even lead to confusion. Let's look at some situations where you'll need to assist iOS in making components fully accessible.

## Making System Components Without Text/Title Properties Fully Accessible

It's clear to us after seeing how a `UISlider` is read aloud to a user that we need to do a bit more work to ensure the component has a clear **name** to inform the user what exactly they're adjusting. Remember that the **name** is represented by the component's `accessibilityLabel`, so we only need one line of code:
```swift
let slider = UISlider()
slider.minimumValue = 0
slider.maximumValue = 10
slider.value = 5
slider.accessibilityLabel = "Volume"
```
The user now hears "Volume, 50%, Adjustable", which correspond to the **name**, **value**, and **role**, respectively.

## Making System Components Represented With Images Fully Accessible

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

## Custom User Interface Components
I should preface this section by saying the following: **where possible, inherit from system components**. If your custom component acts like a system button, segmented control, switch, etc., consider subclassing the corresponding system components, such as `UIButton`, `UISegmentedControl`, `UISwitch`, etc. These components have already been built to be accessible on iOS, so subclassing can both reduce the amount of upfront work necessary to make them accessible and avoid the cost of maintenance for any accessibility changes throughout the years.

### Ensuring Your Interface Components Express Their Affordances

### Grouping Content to Simplify Navigation
When content on screen is visually grouped together, it can be easy for a sighted user to quickly read as a single item and move on, however it may take a user using Assistive Technologies much more effort to read and navigate through the screen if we don't use accessibility APIs to group that content.

Say we've built a custom `UIView` which contains information about a downloadable app. It has an icon, title, description, rating, and price:

![GroupedContentExample](https://user-images.githubusercontent.com/6239518/134990825-7370d591-d184-46b8-89df-968ad96f4531.png)

As a sighted user, the grouping of elements is clear. And if there are multiple elements, your eyes can easily jump between them:

![GroupedContentExample_Multiple](https://user-images.githubusercontent.com/6239518/134991195-c746923c-80ee-4c27-abbc-0fa478423239.png)

Let's see what the experience for a VoiceOver user will be (where "quoted text" is VoiceOver speaking and _italicized text_ is a user action):

"Apple Maps"  
_Swipes right_  
"Navigate and explore the world"  
_Swipes right_  
"Rated 3 out of 5 stars"  
_Swipes right_  
"Free"  
_Swipes right_  
...and so on with the next view

It takes a VoiceOver user **5 swipes per view** to navigate through this user interface. Grouping the content with accessibility APIs will take this from 5 swipes down to 1. To do this, you can overrride the view's `accessibilityElements` property to provide just a single accessibility element:
 
 ```swift
private var _accessibilityElements: [Any]?

override var accessibilityElements: [Any]? {
    get {
        // If we've already generated the elements, return them.
        if let _accessibilityElements = _accessibilityElements {
            return _accessibilityElements
        }
        
        var elements: [UIAccessibilityElement] = []

        // Create a single element representing all the text inside the view.
        let labelsElement = UIAccessibilityElement(accessibilityContainer: self)
        // Define an accessibility frame that captures all the labels so VoiceOver
        // knows where the grouped accessibility element is on screen.
        labelsElement.accessibilityFrameInContainerSpace = self.titleLabel.frame
          .union(self.descriptionLabel.frame)
          .union(self.ratingLabel.frame)
          .union(self.priceLabel.frame)

        // Create an accessibility label that contains the text from each of the labels.
        labelsElement.accessibilityLabel = """
          \(self.titleLabel.text!), \(self.descriptionLabel.text!), \(self.ratingLabel.text!). Price: \(self.priceLabel.text!)
        """
        
        elements.append(labelsElement)

        // Cache the elements.
        self._accessibilityElements = elements

        return elements
    }
    set {
        self._accessibilityElements = newValue
    }
}
```

Let's break this down.

First, you'll notice we're storing a local copy of our accessibility elements inside the property `private var _accessibilityElements: [Any]?` and returning them whenever `accessibilityElements` is accessed, if they exist. The reason for this caching mechanism is the VoiceOver expects a consistent array of accessibility elements when navigating. If you _didn't_ implement this cache, you user would end up in an infinite loop when attempting to swipe through the first view because the `accessibilityElements` array will keep getting regenerated.

Next, we're creating a single `UIAccessibilityElement` to represent all of our labels. This will allow the user to perform a single swipe to navigate over our view. We set the `accessibilityFrameInContainerSpace` property to be a union of all the label frames. This ensures VoiceOver knows where our single, grouped element is on screen. Then we set the `accessibilityLabel` of the element so that it reads out the information contained in each of our labels.

The last step caches the accessibility elements array to avoid that infinite VoiceOver loop.

Now the user will hear: "Apple Maps, Navigate and explore the world, Rated 3 out of 5 stars. Price: Free" as a single sentence.

### Making the Contents of Container Views Accessible
