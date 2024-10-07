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


### Fix Difficulties

Setting one of the child views of the TabView as the ID property causes an unexpected crash when updating the data. This may be due to a local set up though, as haven't tested fully.

Assigning the whole TabView with the ID causes the whole thing to reload and revert back to tab 1. 

To avoid this, the id needs to be assigned to something on the actual view that needs the update. This could be done by any @State property, but seems to make sense to assign it to the TabViewManager. To separate view logic with this wider issue.




## Non-Working Fix Ideas:

