# Nested Observable Objects

### Problem:

An **@ObservableObject** in **SwiftUI** is supposed to publish changes to any properties marked with **@Published** to any **SwiftUI** view accessing the property, causing the view to reload and update.

When using an @ObservableObject class within another @ObservableObject class (nesting them) this results in the child object publishing updates to the parent object, however the parent object does not publish those changes to the view or up the hierarchy.

### Contents: 
- [Fix 1](#fix-1)
- [Fix 2](#fix-2)

### Bug Example:


```swift
import SwiftUI

struct ContentView: View {
    @StateObject var parentObject = ParentObject(name: "Parent Object")
    var body: some View {
        VStack {
            Text("Value: \(parentObject.childObject.value)")
            
            Button("Increase Value") {
                parentObject.childObject.value += 1
                print(parentObject.childObject.value)
            }
            
        }
    }
}

#Preview {
    ContentView()
}

class ParentObject: ObservableObject {
    
    init(name: String) {
        self.name = name
        self.childObject = ChildObject()
    }
    
    @Published var name: String
    @Published var childObject: ChildObject
}


class ChildObject: ObservableObject {
    @Published var value: Int = 0
}
```

When pressing the **Button** in the above code the view doesn't update the **Text** view accordingly. This can be verified by checking that the **Print** statement fires in the **Terminal**. 


## Fix 1

The simplest and easiest solution is to manually call the **ParentObject**'s [objectWillChange](https://developer.apple.com/documentation/combine/observableobject/objectwillchange-2oa5v) publisher, such as with
```parentObject.objectWillChange.send()```.

### Fix Example:

```swift
struct ContentView: View {
    @StateObject var parentObject = ParentObject(name: "Parent Object")
    var body: some View {
        VStack {
            Text("Value: \(parentObject.childObject.value)")
            
            Button("Increase Value") {
                parentObject.childObject.value += 1
                parentObject.objectWillChange.send()
            }

        }
    }
}
```

## Fix 2

To avoid having to manually call the parent [objectWillChange](https://developer.apple.com/documentation/combine/observableobject/objectwillchange-2oa5v) publisher and risk forgetting to do so, the **ChildObject** can instead be converted into a Struct.


### Fix Example:

```swift
struct ChildObject {
    var value = 0
}
```

If there are numerous properties being called frequently from multiple locations then this may be a better options. The **ChildObject** loses the functionality of operating independently as an **@ObservableObject**, but your **ParentObject** will essentially run normally and you won't needed to add any extra code elsewhere.