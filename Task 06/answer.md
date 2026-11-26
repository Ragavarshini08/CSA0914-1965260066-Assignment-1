# TASK 6 – Design the Inheritance Hierarchy

**Bloom’s Level: K4 – Analyse**

**Question:** Design an inheritance hierarchy for the vehicles and, if appropriate, for the users (student, staff, visitor).

## Answer

### 1. Inheritance Hierarchy

Inheritance is a Java feature that allows one class to acquire the properties and methods of another class.

For the parking management system, `Vehicle` is the superclass, and `Car`, `Bike` and `ElectricCar` are subclasses.

**Superclass: Vehicle**

Fields:
- `String vehicleNo`
- `String vehicleType`
- `double parkingRate`

Methods:
- `displayDetails()`
- `calculateFee()`

**Subclasses:**
1. **Car:** Inherits common vehicle details and adds `int numberOfDoors`.
2. **Bike:** Inherits common vehicle details and adds `int engineCC`.
3. **ElectricCar:** Inherits the properties of Car and adds `double batteryCapacity`.

This design uses:
- **Hierarchical Inheritance:** Car and Bike inherit from Vehicle.
- **Multilevel Inheritance:** ElectricCar inherits from Car, which inherits from Vehicle.

### 2. What Is Inherited and What Is Added?

- **Vehicle:** Contains common vehicle information such as registration number, type and parking rate.
- **Car:** Inherits vehicle number, vehicle type and parking rate. Adds the number of doors.
- **Bike:** Inherits common vehicle properties from Vehicle. Adds engine capacity.
- **ElectricCar:** Inherits vehicle properties from Vehicle through Car. Adds battery capacity and other electric vehicle details.

Inheritance avoids code duplication and improves code reusability.

### 3. Use of `super` Keyword

The `super` keyword refers to the immediate parent class.

It is used in three ways:

**A. Accessing Parent Class Constructor**

```java
super(vehicleNo, vehicleType, parkingRate);
```

It calls the parent class constructor to initialize inherited fields.

**B. Accessing Parent Class Method**

```java
super.displayDetails();
```

It calls the parent class method from the subclass.

**C. Accessing Parent Class Field**

```java
super.vehicleNo;
```

It accesses the parent class field when required.

### 4. Java Program Demonstrating Inheritance

```java
class Vehicle {
    String vehicleNo;
    String vehicleType;
    double parkingRate;

    Vehicle(String vehicleNo, String vehicleType, double parkingRate) {
        this.vehicleNo = vehicleNo;
        this.vehicleType = vehicleType;
        this.parkingRate = parkingRate;

        System.out.println("Vehicle Constructor");
    }

    void displayDetails() {
        System.out.println("Vehicle No: " + vehicleNo);
        System.out.println("Vehicle Type: " + vehicleType);
    }
}

class Car extends Vehicle {
    int numberOfDoors;

    Car(String vehicleNo, double parkingRate, int doors) {
        super(vehicleNo, "Car", parkingRate);
        this.numberOfDoors = doors;

        System.out.println("Car Constructor");
    }
}

class ElectricCar extends Car {
    double batteryCapacity;

    ElectricCar(String vehicleNo, double rate, int doors, double battery) {
        super(vehicleNo, rate, doors);
        this.batteryCapacity = battery;

        System.out.println("ElectricCar Constructor");
    }
}

public class Main {
    public static void main(String[] args) {
        ElectricCar e = new ElectricCar("TN01AB1111", 20, 4, 75);

        e.displayDetails();

        System.out.println("Doors: " + e.numberOfDoors);
        System.out.println("Battery: " + e.batteryCapacity + " kWh");
    }
}
```

### 5. Constructor Execution Order (Trace)

When an ElectricCar object is created, constructors execute from the superclass to the subclass.

Example:

```java
ElectricCar e = new ElectricCar("TN01AB1111", 20, 4, 75);
```

Execution order:
1. ElectricCar constructor is called.
2. `super()` calls the Car constructor.
3. Car constructor calls the Vehicle constructor using `super()`.
4. Vehicle constructor executes first.
5. Car constructor executes next.
6. ElectricCar constructor executes last.

**Output:**

```text
Vehicle Constructor
Car Constructor
ElectricCar Constructor
Vehicle No: TN01AB1111
Vehicle Type: Car
Doors: 4
Battery: 75.0 kWh
```

### 6. Conclusion

Inheritance allows common vehicle properties and methods to be reused by different vehicle classes. Hierarchical and multilevel inheritance reduce code duplication. The `super` keyword helps access parent class constructors, methods and fields. Therefore, inheritance makes the parking management system easier to extend and maintain.
