# TvOS-Debug-Hints

This is a list of hints about how to debug TvOS project more efficient! 

### 1) LLDB
#### 1a)UIFocusDebugger
[Class](https://developer.apple.com/documentation/uikit/uifocusdebugger) for debugging focus in LLDB.
```swift
po UIFocusDebugger.checkFocusability(for: yourView)  // Diagnoses potential issues.
po UIFocusDebugger.status() // Check other methods of this class.
po UIFocusDebugger.simulateFocusUpdateRequest(from: environment) // i.e. a successful call to environment.setNeedsFocusUpdate().
po UIFocusDebugger.help() // Prints detailed instructions for all debugging commands.
```
#### 1b) _whyIsThisViewNotFocusable [Deprecated]
```swift 
po yourView.perform(Selector("_whyIsThisViewNotFocusable")) // swift 5.0
po yourView.performSelector(Selector("_whyIsThisViewNotFocusable")) // swift 3.x
po [(UIView *)0x148db5234 _whyIsThisViewNotFocusable] // 0x148db5234 is the address of your object.
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

### 5) Understanding focus updates
Another way that focus can change is when user interaction happens (a swipe in a specific direction for example). When this happens, the focus engine builds a list of possible focusable items in the direction of control. It checks the canBecomeFocused property of these items to filter the list and then finally picks the one that is nearest to the original focused item. Once an item is chosen, the focus engine then tries to determine if the focus change is allowed. This is done by calling shouldUpdateFocus(in:) to see if any of the affected items returns false. The order of these calls is interesting. First, the item that is losing focus has its shouldUpdateFocus(in:) called. Next the system traverses up the hierarchy, calling shouldUpdateFocus(in:) on each parent until it reaches the root. Finally it calls shouldUpdateFocus(in:) on the item that is receiving focus. The system will then traverse up the hierarchy for the receiving focus item, but will only call shouldUpdateFocus(in:) on the hierarchy items that it didn’t call while traversing for the item losing focus.

To visualize the order of these calls, given the following view and the user has swiped in the down direction:
![](https://www.bignerdranch.com/assets/img/blog/2017/03/shouldupdatefocusinsameview.png "")  
The order of shouldUpdateFocus(in:) will be:

Top Left -> LeftSubView -> ViewController -> Bottom Left

But if the user swipes in the right direction:  
![](https://www.bignerdranch.com/assets/img/blog/2017/03/shouldupdatefocusindifferentview.png "")  
The order will be:

Top Left -> LeftSubView -> ViewController -> Top Right -> RightSubView


### Docs and sources
* https://developer.apple.com/library/archive/documentation/General/Conceptual/AppleTV_PG/WorkingwiththeAppleTVRemote.html
* https://developer.apple.com/documentation/uikit/focus-based_navigation/debugging_focus_issues_in_your_app
* https://www.bignerdranch.com/blog/10-tips-for-mastering-the-focus-engine-on-tvos/
* https://devstreaming-cdn.apple.com/videos/wwdc/2017/224sn8vw625k1e86/224/224_focus_interaction_in_tvos_11.pdf?dl=1
