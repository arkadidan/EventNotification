# EventNotification

Convenience on top of NotificationCenter. Sends _typed_ values (one to many) to all subscribed observers. Takes care and utomagically unsubscribes from NotificationSenter.


```swift
struct A {
    let string: String
}

extension A: Event {}

class SomeEventObserver: EventObserver {
    let eventToken = EventToken() // protocol provides default token. Declaring this property allows unsubscribing at any time. Default token automatically unsubscribe on deinit.

    func startObservingA() {
        handleEvent(A.self) { [weak self] a in
            print(a.string)
        }
    }
    
    func stopObservingA() {
        self.unsubscribe(A.self)
    }
    
    func stopObservingAll() {
        self.unsubscribe()
    }
}

let a = A(string: "a event")
a.send()
```


```swift
class OtherEventObserver: DefaultEventObserver {

    func startObservingA() {
        handleEvent(A.self) { [weak self] a in
            print(a.string)
        }
    }
}
```

### Keyboard

Implementaion of keyboard events

`KeyboardDidShowEvent`, `KeyboardWillShowEvent`, `KeyboardWillHideEvent`, `KeyboardWillChangeFrameEvent`

```swift
class ViewControllerController: UIViewController, KeyboardEventObserver {

    let eventToken = EventToken()

    override func viewDidAppear(_ animated: Bool) {
        super.viewDidAppear(animated)

        onKeyboardWillAppear { [weak self] frame in
            var contentInsets: UIEdgeInsets = .zero
            contentInsets.bottom = frame.height
            self?.tableView.contentInset = contentInsets
            self?.tableView.scrollIndicatorInsets = contentInsets
        }

        onKeyboardDissappear { [weak self] in
            self?.tableView.contentInset = .zero
            self?.tableView.setContentOffset(.zero, animated: true)
        }
        
        onKeyboardFrameChanged { [weak self] frame in
            // new frame
        }
    }
    
    override func viewDidDisappear(_ animated: Bool) {
        super.viewDidDisappear(animated)
        
        unsubscribeKeyboardAppear()
        unsubscribeKeyboardDisappear()
        
        // or,
        unsubscribe()
    }
}
```


```ruby
pod 'EventNotification'
```
