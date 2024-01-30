# Nested Observable Objects

### Problem:

An @ObservableObject in SwiftUI is supposed to publish changes to any properties marked with @Published to any SwiftUI view accessing the property, causing the view to reload and update.

When using an @ObservableObject class within another @ObservableObject class (nesting them) this results in the child object publishing updates to the parent object, however the parent object does not publish those changes to the view or up the hierarchy.

### Contents: 
- [Solution 1](#solution-1)
- [Solution 2](#solution-2)

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

When pressing the Button in the above code the view doesn't update the Text view accordingly. This can be verified by checking that the Print statement fires in the Terminal. 


## Solution 1

The simplest and easiest solution is to manually call the parent object's [objectWillChange](https://developer.apple.com/documentation/combine/observableobject/objectwillchange-2oa5v) publisher, such as with
```parentObject.objectWillChange.send()```.

### Solution Example:

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

## Solution 2

To avoid having to manually call the parent [objectWillChange](https://developer.apple.com/documentation/combine/observableobject/objectwillchange-2oa5v) publisher and risk forgetting to do so, the ChildObject can instead be converted into a Struct.


### Solution Example:

```swift
struct ChildObject {
    var value = 0
}
```
