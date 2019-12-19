# TvOS-Debug-Hints

This is a list of hints about how to debug TvOS project more efficient! 

## LLDB
### _whyIsThisViewNotFocusable
```swift 
po yourView.perform(Selector("_whyIsThisViewNotFocusable")) // swift 5.0
po self.view!.performSelector(Selector("_whyIsThisViewNotFocusable")) // swift 3.x
po [(UIView *)0x148db5234 _whyIsThisViewNotFocusable] // 0x148db5234 is the address of your object
```

### UIFocusDebugger
[Class](https://developer.apple.com/documentation/uikit/uifocusdebugger) for debugging focus in LLDB.
```swift
po UIFocusDebugger.status()
```
## Focus logging
In your Xcode project, select Edit Scheme and add -UIFocusLoggingEnabled YES to the Arguments Passed On Launch section.

![alt text](https://docs-assets.developer.apple.com/published/ea1ec98ffa/c72f8821-e358-4f59-b93a-d87662c91d19.png
 "Logo Title Text 1")

## Docs
* https://developer.apple.com/library/archive/documentation/General/Conceptual/AppleTV_PG/WorkingwiththeAppleTVRemote.html
* https://developer.apple.com/documentation/uikit/focus-based_navigation/debugging_focus_issues_in_your_app
