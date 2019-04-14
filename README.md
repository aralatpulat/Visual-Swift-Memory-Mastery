# Visual Swift Memory Mastery by Mark Moeykens - Notes

**Chapter 2 - Value Types and Reference Types**

&ensp;&ensp;Value types are string, int, struct, enum, bool, array and dictionary. Keeps an unique copy of data. When we pass value type into a function, it creates a copy of that instance. So another memory space allocated. Swift remove value types easily from memory

&ensp;&ensp;Reference types are single instance, only one shared copy exist. Reference types usually defined as a class, We only pass pointer or reference back to single copy of the variable. When we create an instance of a class, it creates a variable with a reference pointing back to the class (Basically referencing to the memory address). If a class has multiple pointer and one of its value changed, all references that pointing the same address change. Xcode shows memory address of reference types beginning with 0x. Generally reference types cause a memory problem.

```Swift
//MyClass is reference type
//myInt is value type  

Class MyClass {
	var myInt = 8
}

let first = MyClass() //address: 0x60022
let second = first //address: 0x60022
second.myInt = 5
print(second.myInt) //5
print(first.myInt) //5
``` 

&ensp;&ensp;We can see reference types from Xcode debugger. They have an address which starts with 0x (for example UIViewController, UILabel, NSObjectProtocol). Value types has no address in debugger window.  

&ensp;&ensp;In debugger window, right click to the reference type and say “Print Description of 'variable’”. It shows you values in console window that we might be interested in.  

**Chapter 3 - Finding and Fixing Memory Leaks**

&ensp;&ensp;When there are no variables pointing to the reference type, swift can remove the reference type safely from the memory. 

&ensp;&ensp;Swift uses Automatic Reference Counting(ARC) to count references. Object that has references cannot be removed from the memory.  

&ensp;&ensp;Deinitializer (deinit) can be used to demonstrate class instance deallocation (frees up the memory) from the memory.  

&ensp;&ensp;Retain cycle (also called reference cycle, retention cycle, circular reference) means two instances referencing each other and they retains. It causes memory leaks and not to be deallocated. In order to break retain cycle (chain of the dependencies), only changing one of the strong reference to weak or unowned reference will be enough.  

&ensp;&ensp;Class referencing to another class is a strong reference. In strong reference, child exist as long as parent exists. So one reference should be weak or unowned reference to avoid retain cycle. Weak and unowned does not increment reference count.  

>_**Weak reference:**_ Child (reference) my or my not exist. Can be optional value.  

>**_Unowned reference:_** Child (reference) exist all the time until parent removed. We can’t set it to nil because it is required. Always has a value.  

>_**Tools to detect memory leaks:**_
&ensp;&ensp;Product -> Profile-> Leaks: tool that shows allocations, leaks, cycles etc. (should clean the project before recording)
&ensp;&ensp;Debug memory graph and debug navigator can be used to see memory allocations and dependencies that might cause memory leak.  

**Chapter 4 - Fixing Memory Leaks in Closures**

&ensp;&ensp;Closures are reference types. They are basically anonymous functions. It has own address and memory. When you pass closure around, you basically pass a pointer to the same address. Closures might create retain cycles and memory leaks.  

&ensp;&ensp;Closures capture values. A nested function can capture any of its outer function's arguments, constants and variables. Parent view controller as “self” inside its block for example.  

&ensp;&ensp;Even if the variable declared as weak, it will be strong reference in closure when going through in capture list. If necessary weak or unowned should be used before variable name to prevent retain cycle.  

```Swift
//[weak self] -> capture list 
viewModel.getCoins { [weak self] () in
	DispatchQueue.main.async {
		self?.coinTableView.reloadData()
		self?.removeLoading()
	}
}
```  

**Chapter 5 - Notification Center and Memory**  

&ensp;&ensp;Tab bar controller doesn’t deallocate its view controllers, it holds array of loaded view controller for the life of the app.  

&ensp;&ensp;NotificationCenter might cause retain cycle because we pass a closure.

&ensp;&ensp;NotificationCenter is a singleton. So even view controller removed from memory along with all the observers that we created, NotificationCenter stays in memory. Observers have strong, weak or unowned reference to the deallocated view controller. Strong and unowned references might throw error in this situation.  

**Chapter 7 - Callbacks and Memory**  

&ensp;&ensp;Setting a reference to nil decrements reference count.  

**Chapter 8 - Delegates and Memory**  

&ensp;&ensp;Delegate property of a variable hold a reference to view controller (if delegates a vc). Making delegate variable as weak will solve the problem. Be careful if the delegate variable is reference type not value type.  

&ensp;&ensp;Make protocol inherit from AnyObject to restricted that protocol to just classes (reference type) so we can make delegate weak.
