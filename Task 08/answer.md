# TASK 8 – Apply Polymorphism

## 1. Polymorphism in Java

Polymorphism means "many forms". It allows the same method name or reference to perform different operations depending on the situation.

Java supports two types of polymorphism:

1. **Compile-time Polymorphism:** Achieved through method overloading. The compiler selects the appropriate method based on the arguments.
2. **Runtime Polymorphism:** Achieved through method overriding. The JVM selects the appropriate overridden method based on the actual object type at runtime.

## 2. Compile-Time Polymorphism (Method Overloading)

Method overloading means defining multiple methods with the same name but different parameter lists.

In the parking management system, `calculateFee()` can be overloaded to accept parking duration in minutes or entry and exit times.

```java
import java.time.LocalTime;
import java.time.Duration;

class ParkingFee {

    double calculateFee(int minutes) {
        int hours = (minutes + 59) / 60;
        return hours * 30;
    }

    double calculateFee(LocalTime in, LocalTime out) {
        long minutes = Duration.between(in, out).toMinutes();
        return calculateFee((int) minutes);
    }
}
```

**Explanation:**
- `calculateFee(int minutes)` calculates the fee using parking duration.
- `calculateFee(LocalTime in, LocalTime out)` calculates the duration using entry and exit times.
- The compiler selects the correct method based on the arguments passed.

This is called compile-time polymorphism.

## 3. Runtime Polymorphism (Method Overriding)

Runtime polymorphism is achieved through inheritance and method overriding.

A single array of Vehicle references can store different types of vehicle objects.

```java
Vehicle[] parked = {
    new TwoWheeler("TN01A1"),
    new Car("TN02B2"),
    new ElectricCar("TN03C3")
};
```

Here:
- Vehicle is the superclass.
- TwoWheeler, Car and ElectricCar are subclasses.
- All objects are stored in one Vehicle array.

This is called **upcasting**, where subclass objects are referred to using a superclass reference.

When `calculateFee()` is called, Java executes the overridden method based on the actual object type. This is called dynamic method dispatch.

## 4. Dynamic Method Dispatch Example

Consider the statement:

```java
Vehicle v = new Car("TN02B2");
double fee = v.calculateFee(130);
```

**Execution:**
1. Declared reference type: Vehicle.
2. Actual object type: Car.
3. The calculateFee() method is called.
4. Java executes the Car-specific ratePerHour() method.
5. Parking duration is 130 minutes, which is charged as 3 started hours.
6. Fee = 3 × 30 = ₹90.

Therefore, the correct method is selected at runtime without checking the vehicle type manually.

## 5. Advantages of Polymorphism

1. Reduces long if-else and switch statements.
2. Improves code reusability.
3. Makes the program easier to maintain.
4. Allows different vehicle types to use the same method name with different implementations.
5. Makes it easier to add new vehicle types without changing existing code.

## 6. Complete Java Program

**File name: FeeDemo.java**

```java
import java.time.LocalTime;
import java.time.Duration;

interface Chargeable {
    double chargingFee(int kWh);
}

class ParkingFee {

    double calculateFee(int minutes) {
        int hours = (minutes + 59) / 60;
        return hours * 30;
    }

    double calculateFee(LocalTime in, LocalTime out) {
        long minutes = Duration.between(in, out).toMinutes();
        return calculateFee((int) minutes);
    }
}

abstract class Vehicle {

    private final String regNo;

    protected Vehicle(String regNo) {
        this.regNo = regNo;
    }

    public String getRegNo() {
        return regNo;
    }

    protected abstract double ratePerHour();

    public double calculateFee(int minutes) {
        int hours = (minutes + 59) / 60;
        return hours * ratePerHour();
    }

    @Override
    public boolean equals(Object o) {
        return o instanceof Vehicle &&
            regNo.equals(((Vehicle) o).regNo);
    }

    @Override
    public int hashCode() {
        return regNo.hashCode();
    }
}

class TwoWheeler extends Vehicle {

    TwoWheeler(String regNo) {
        super(regNo);
    }

    @Override
    protected double ratePerHour() {
        return 10;
    }
}

class Car extends Vehicle {

    Car(String regNo) {
        super(regNo);
    }

    @Override
    protected double ratePerHour() {
        return 30;
    }
}

class ElectricCar extends Car implements Chargeable {

    ElectricCar(String regNo) {
        super(regNo);
    }

    @Override
    public double chargingFee(int kWh) {
        return kWh * 12;
    }
}

public class FeeDemo {

    public static void main(String[] args) {

        // Compile-time polymorphism
        ParkingFee p = new ParkingFee();
        double fee1 = p.calculateFee(130);
        double fee2 = p.calculateFee(
            LocalTime.of(9, 0),
            LocalTime.of(11, 10)
        );

        // Runtime polymorphism
        Vehicle[] parked = {
            new TwoWheeler("TN01A1"),
            new Car("TN02B2"),
            new ElectricCar("TN03C3")
        };

        for (Vehicle v : parked) {

            double fee = v.calculateFee(130);

            if (v instanceof Chargeable c)
                fee += c.chargingFee(10);

            System.out.println(v.getClass().getSimpleName()
                + " " + v.getRegNo()
                + " -> Rs " + fee);
        }

        System.out.println(
            new Car("TN02B2").equals(
                new ElectricCar("TN02B2")
            )
        );
    }
}
```

## 7. Explanation of the Program

### A. Fee Calculation

The expression:

```java
int hours = (minutes + 59) / 60;
```

Calculates the number of started hours using integer arithmetic.

For 130 minutes:

- Billable hours = (130 + 59) / 60
- Billable hours = 189 / 60
- Billable hours = 3

### B. Parking Fee

- TwoWheeler: 3 × 10 = ₹30.
- Car: 3 × 30 = ₹90.
- ElectricCar: (3 × 30) + (10 × 12) = ₹210.

### C. Chargeable Interface

The Chargeable interface defines the `chargingFee()` method.

ElectricCar implements this interface and calculates charging charges at ₹12 per kWh.

### D. instanceof Operator

```java
if (v instanceof Chargeable c)
    fee += c.chargingFee(10);
```

It checks whether the vehicle supports the Chargeable interface. If true, the charging fee is added.

## 8. Expected Output

```text
TwoWheeler TN01A1 -> Rs 30.0
Car TN02B2 -> Rs 90.0
ElectricCar TN03C3 -> Rs 210.0
true
```

## 9. Why Does the Last Line Print true?

The last statement compares:

```java
new Car("TN02B2").equals(
    new ElectricCar("TN02B2")
);
```

It prints true because:

1. Both objects have the same registration number, TN02B2.
2. The equals() method compares only the registration numbers.
3. Therefore, both objects are considered equal.

This is correct if the registration number is the unique identity of a vehicle across the entire parking system.

## 10. Alternative Concept: instanceof Checks Instead of Dynamic Method Dispatch

Instead of overriding methods, the program can use instanceof checks and type casting to identify each vehicle type.

```java
double fee;

if (v instanceof ElectricCar) {
    fee = 3 * 30 + 10 * 12;
}
else if (v instanceof Car) {
    fee = 3 * 30;
}
else if (v instanceof TwoWheeler) {
    fee = 3 * 10;
}
else {
    fee = 0;
}
```

## Conclusion

Polymorphism supports both method overloading and method overriding. Compile-time polymorphism selects methods based on arguments, while runtime polymorphism selects methods based on the actual object type. It improves code reusability, flexibility and maintainability in the parking management system.
