# Swift 和 JavaScript 比较片段(9) —继承

> 原文：<https://medium.com/hackernoon/swift-and-javascript-comparison-snippets-9-inheritance-a532351d0e57>

![](img/688d160890251648606ff6059c1d2b36.png)

迅速发生的

```
// Defining a Base Class
class Vehicle {
    var currentSpeed = 0.0
    var description: String {
        return "traveling at \(currentSpeed) miles per hour"
    }
    func makeNoise() {
        // do nothing - an arbitrary vehicle doesn't necessarily make a noise
    }
}
let someVehicle = Vehicle()
print("Vehicle: \(someVehicle.description)")
// Vehicle: traveling at 0.0 miles per hour // Subclassing
class SomeSubclass: SomeSuperclass {
    // subclass definition goes here
}
class Bicycle: Vehicle {
    var hasBasket = false
}
let bicycle = Bicycle()
bicycle.hasBasket = truebicycle.currentSpeed = 15.0
print("Bicycle: \(bicycle.description)")
// Bicycle: traveling at 15.0 miles per hour // Subclasses can themselves be subclassed
class Tandem: Bicycle {
    var currentNumberOfPassengers = 0
}
let tandem = Tandem()
tandem.hasBasket = true
tandem.currentNumberOfPassengers = 2
tandem.currentSpeed = 22.0
print("Tandem: \(tandem.description)")
// Tandem: traveling at 22.0 miles per hour // Overriding
class Train: Vehicle {
    override func makeNoise() {
        print("Choo Choo")
    }
}
let train = Train()
train.makeNoise()
// Prints "Choo Choo" // Overriding Property Getters and Setters
class Car: Vehicle {
    var gear = 1
    override var description: String {
        return super.description + " in gear \(gear)"
    }
}
let car = Car()
car.currentSpeed = 25.0
car.gear = 3
print("Car: \(car.description)")
// Car: traveling at 25.0 miles per hour in gear 3
```

Java Script 语言

```
// Defining a Base Class
class Vehicle {
    constructor() {
        this.currentSpeed = 0.0
    }
    get description() {
        return `traveling at ${currentSpeed} miles per hour`
    }
    function makeNoise() {
        // do nothing - an arbitrary vehicle doesn't necessarily make a noise
    }
}
let someVehicle = Vehicle()
console.log(`Vehicle: ${someVehicle.description}`)
// Vehicle: traveling at 0.0 miles per hour // Subclassing
class SomeSubclass extends SomeSuperclass {
    contructor() {
        super();
    }
    // subclass definition goes here
}
class Bicycle extends Vehicle {
    contructor() {
        super();
        this.hasBasket = false
    }
}
let bicycle = Bicycle()
bicycle.hasBasket = truebicycle.currentSpeed = 15.0
console.log(`Bicycle: ${bicycle.description}`)
// Bicycle: traveling at 15.0 miles per hour // Subclasses can themselves be subclassed
class Tandem extends Bicycle {
    contructor() {
        super();
        this.currentNumberOfPassengers = 0
    }
}
let tandem = Tandem()
tandem.hasBasket = true
tandem.currentNumberOfPassengers = 2
tandem.currentSpeed = 22.0
console.log("Tandem: \(tandem.description)")
// Tandem: traveling at 22.0 miles per hour // Overriding
class Train extends Vehicle {
    contructor() {
        super();
        this.currentNumberOfPassengers = 0
    }
    function makeNoise() {
        console.log("Choo Choo")
    }
}
let train = Train()
train.makeNoise()
// Prints "Choo Choo" // Overriding Property Getters and Setters
class Car extends Vehicle {
    contructor() {
        super();
        this.gear = 1
    }
    get description() {
        return `${super.description} in gear ${gear}`
    }
}
let car = Car()
car.currentSpeed = 25.0
car.gear = 3
console.log(`Car: ${car.description}`)
// Car: traveling at 25.0 miles per hour in gear 3
```

# 更多 [Swift 和 JavaScript 比较片段](/@unbug/swift-and-javascript-comparison-snippets-ab39171b85fd)