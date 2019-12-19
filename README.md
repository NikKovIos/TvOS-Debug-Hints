# TvOS-Debug-Hints

This is a list of hints about how to debug TvOS project more efficient! 

### 1) LLDB
#### 1a) _whyIsThisViewNotFocusable
```swift 
po yourView.perform(Selector("_whyIsThisViewNotFocusable")) // swift 5.0
po yourView.performSelector(Selector("_whyIsThisViewNotFocusable")) // swift 3.x
po [(UIView *)0x148db5234 _whyIsThisViewNotFocusable] // 0x148db5234 is the address of your object.
```

#### 1b)UIFocusDebugger
[Class](https://developer.apple.com/documentation/uikit/uifocusdebugger) for debugging focus in LLDB.
```swift
po UIFocusDebugger.status() // Check other methods of this class.
```

### 2) UIFocusUpdateContext
> UIKit sends a visual representation of how the focus engine searched for the next view to be focused in the Quick Look for UIFocusUpdateContext instances. To see this image, set a breakpoint in either shouldUpdateFocusInContext: or didUpdateFocusInContext:withAnimationCoordinator:. When the breakpoint is hit during execution of your app, select the context parameter in the debugger and open Quick Look.
![](https://developer.apple.com/library/archive/documentation/General/Conceptual/AppleTV_PG/Art/ContextParameter_2x.png "")  
> If the breakpoint was hit during a focus movement (not a programmatic focus update), then Quick Look presents an image to show the search path that the focus engine used to find the next focused view. Figure 3-5 shows an example.
![](https://developer.apple.com/library/archive/documentation/General/Conceptual/AppleTV_PG/Art/QuickLookPresentationOfImage_2x.png "")  

### 3) Focus logging
In your Xcode project, select Edit Scheme and add -UIFocusLoggingEnabled YES to the Arguments Passed On Launch section.

![](https://docs-assets.developer.apple.com/published/ea1ec98ffa/c72f8821-e358-4f59-b93a-d87662c91d19.png
 "")
 
### 4) Listening to the notification
Add in viewDidLoad:
```swift
if #available(tvOS 11.0, *) {
    NotificationCenter.default.addObserver(forName: UIFocusSystem.movementDidFailNotification, object: nil, queue: .main) { [weak self] notification in
        let context = notification.userInfo![UIFocusSystem.focusUpdateContextUserInfoKey] as! UIFocusUpdateContext
        print(context) // If you add a breakpoint here you can quicklook the context in the debugger for more information
        print(UIFocusDebugger.checkFocusability(for: self!.collectionView)) // replace collectionView with the view you want to check
    }
}
``` 

### Docs
* https://developer.apple.com/library/archive/documentation/General/Conceptual/AppleTV_PG/WorkingwiththeAppleTVRemote.html
* https://developer.apple.com/documentation/uikit/focus-based_navigation/debugging_focus_issues_in_your_app
