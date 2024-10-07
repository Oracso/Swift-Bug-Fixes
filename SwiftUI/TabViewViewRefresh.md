# TabView View Refresh

### Problem:

When underlying data is changed, views shown within a TabView don't seem to be updated automatically. i.e. any .onAppear() modifiers will only run initially when the TabView is loaded, or again if something within the view causes it to refresh, any external updates don't seem to refresh the view.


### Contents: 
- [Fix 1](#fix-1)
<!-- - [Fix 2](#fix-2) -->

### Bug Example:


```swift
import SwiftUI

struct ContentView: View {
    var body: some View {
        TabView {
            ViewOne()
                .tabItem {
                    Label("One", systemImage: "plus")
                }
            ViewTwo()
                .tabItem {
                    Label("Two", systemImage: "plus")
                }
        }
    }
}

#Preview {
    ContentView()
}
```


## Fix 1

One solution which may be combined with other enhanced TabView functionality, is to use a **TabViewManager** EnvironmentObject to manually cause child views to refresh.


### Fix Example:

```swift

```






## Non-Working Fix Ideas:

