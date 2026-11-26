# DAY 4 – TASK 12: Re-evaluate and Adapt the Design

**Bloom’s Level: K5 – Evaluate**

## Changed Requirement

The university introduces two new vehicle categories – e-bikes (charged a flat fee) and delivery vans (charged at double the car rate during 9 AM – 5 PM). Visitors may now buy a one-day pass. The facility is extended from 3 to 5 floors with a different number of slots on each floor.

## A. Impact Analysis

The new requirements introduce two additional vehicle categories, a one-day pass system and an expansion from 3 to 5 floors.

### Changes from the Original Problem

1. **New Vehicle Categories:** E-bikes must pay a flat parking fee, while delivery vans must pay double the car rate between 9 AM and 5 PM.
2. **One-Day Pass:** Visitors can purchase a pass that allows parking for one day.
3. **Parking Expansion:** The facility now has 5 floors, and each floor has a different number of parking slots.

### Affected Classes, Methods and Data Structures

- **Vehicle:** Must support the new vehicle categories through inheritance.
- **ParkingFacility:** Must support 5 floors and different slot counts.
- **ParkingTicket:** Must store pass details and parking information.
- **calculateFee():** Must support flat fees and time-based special rates.
- **ParkingSlot:** Its allocation and release logic can remain mostly unchanged.

### Why Is the Array Design Affected?

A normal two-dimensional array assumes the same number of columns for every row. Since each floor has a different number of slots, a jagged array is more suitable.

```java
String[][] slots = new String[5][];

slots[0] = new String[5];
slots[1] = new String[8];
slots[2] = new String[6];
slots[3] = new String[10];
slots[4] = new String[7];
```

Each row represents a floor, and each row can have a different number of slots.

Therefore, the original design requires modifications to the vehicle hierarchy, pricing logic, ticket system and parking array.

## B. Evaluation of the Original Design

The original object-oriented design is suitable for the basic parking system, but it needs some modifications to satisfy the new requirements.

1. **Adding New Vehicle Types:** New subclasses can be created, but adding special pricing rules to every subclass may increase code duplication. A common pricing abstraction is more flexible.
2. **Pricing Rules:** If pricing is implemented separately inside every vehicle class, maintaining different rates becomes difficult. Keeping pricing rules in separate classes makes the system easier to maintain.
3. **Existing Tested Classes:** Classes such as Car, Bike and Vehicle should remain unchanged wherever possible. New functionality should be added through new subclasses and interfaces.
4. **Data Structure:** The original parking array must be modified into a jagged array to support different slot counts on each floor.

**Evaluation:** The original design is partially appropriate. It follows inheritance, encapsulation and polymorphism, but the changed requirements make a small redesign necessary.

## C. Adaptation or Redesign (2 Marks)

**Decision: Modify the original design.**

The revised design uses inheritance, interfaces, method overriding and a jagged array.

### Revised Class Structure

- **Vehicle:** Abstract parent class containing registration number and common vehicle information.
- **Car:** Existing car category.
- **Bike:** Existing bike category.
- **ElectricBike:** New category with a flat parking fee.
- **DeliveryVan:** New category with double the car rate during 9 AM to 5 PM.
- **Pass:** Interface representing the one-day parking pass.
- **OneDayPass:** Implements the Pass interface.
- **ParkingFacility:** Manages the five floors using a jagged array.
- **ParkingTicket:** Stores vehicle and ticket details.

### Java Implementation

**File name: Main.java**

```java
import java.time.LocalTime;

abstract class Vehicle {

    String regNo;

    Vehicle(String regNo) {
        this.regNo = regNo;
    }

    abstract double calculateFee(int hours);
}

class Car extends Vehicle {

    Car(String regNo) {
        super(regNo);
    }

    double calculateFee(int hours) {
        return hours * 30;
    }
}

class Bike extends Vehicle {

    Bike(String regNo) {
        super(regNo);
    }

    double calculateFee(int hours) {
        return hours * 10;
    }
}

class ElectricBike extends Vehicle {

    ElectricBike(String regNo) {
        super(regNo);
    }

    double calculateFee(int hours) {
        return 25;
    }
}

class DeliveryVan extends Vehicle {

    DeliveryVan(String regNo) {
        super(regNo);
    }

    double calculateFee(int hours) {
        return hours * 30;
    }

    double calculateFee(int hours, LocalTime time) {

        if (!time.isBefore(LocalTime.of(9, 0)) &&
            time.isBefore(LocalTime.of(17, 0))) {

            return hours * 60;
        }

        return calculateFee(hours);
    }
}

interface Pass {

    double getPassFee();

    int getValidityHours();
}

class OneDayPass implements Pass {

    public double getPassFee() {
        return 100;
    }

    public int getValidityHours() {
        return 24;
    }
}

class ParkingFacility {

    String[][] slots = new String[5][];

    ParkingFacility() {

        slots[0] = new String[5];
        slots[1] = new String[8];
        slots[2] = new String[6];
        slots[3] = new String[10];
        slots[4] = new String[7];
    }

    void displaySlots() {

        for (int i = 0; i < slots.length; i++) {

            int count = 0;

            for (int j = 0; j < slots[i].length; j++) {

                if (slots[i][j] == null) {
                    count++;
                }
            }

            System.out.println("Floor " + (i + 1) +
                " Available Slots: " + count);
        }
    }
}

public class Main {

    public static void main(String[] args) {

        Vehicle ebike =
            new ElectricBike("TN01EB1234");

        System.out.println("E-Bike Fee: Rs." +
            ebike.calculateFee(3));

        DeliveryVan deliveryVan =
            new DeliveryVan("TN02DV5678");

        System.out.println("Delivery Van Fee (11 AM): Rs." +
            deliveryVan.calculateFee(2, LocalTime.of(11, 0)));

        Pass pass = new OneDayPass();

        System.out.println("One-Day Pass Fee: Rs." +
            pass.getPassFee());

        System.out.println("Pass Validity: " +
            pass.getValidityHours() + " hours");

        ParkingFacility parking = new ParkingFacility();

        parking.displaySlots();
    }
}
```

### Expected Output

```text
E-Bike Fee: Rs.25.0
Delivery Van Fee (11 AM): Rs.120.0
One-Day Pass Fee: Rs.100.0
Pass Validity: 24 hours
Floor 1 Available Slots: 5
Floor 2 Available Slots: 8
Floor 3 Available Slots: 6
Floor 4 Available Slots: 10
Floor 5 Available Slots: 7
```

### Explanation

- E-bike pays a flat fee of ₹25, regardless of parking hours.
- Delivery van pays ₹60 per hour during 9 AM to 5 PM, which is double the car rate of ₹30.
- One-day pass costs ₹100 and remains valid for 24 hours.
- The jagged array supports different parking capacities on all five floors.

**Note:** This is a focused implementation of the changed requirements. The one-day pass fee is treated as a flat fee, and the van's special rate is selected using its entry time.

## D. Evidence-Based Defence

The modified design is more suitable than completely retaining the original design.

### 1. Existing Classes Modified

- Vehicle is retained as the common abstract class.
- Car and Bike remain unchanged.
- ParkingFacility is modified to support five floors using a jagged array.
- ParkingTicket can be extended to record pass information.
- New classes ElectricBike, DeliveryVan and OneDayPass are added.

### 2. Future Vehicle Categories

New vehicle types can be added by extending Vehicle and implementing their own pricing behaviour. This reduces changes to existing classes.

### 3. Test Results

- E-bike for 3 hours: ₹25.
- Delivery van for 2 hours at 11 AM: ₹120.
- One-day pass: ₹100 for 24 hours.
- Five floors successfully support different slot capacities.

These results confirm the new pricing calculations and floor configuration. Further integration tests are required to verify actual vehicle allocation, pass expiry and exit processing.

### 4. Trade-offs

- A jagged array supports unequal floor capacities but requires nested loops to search all slots.
- Separate pricing methods make the system flexible but add a few classes.
- A one-day pass simplifies visitor payments, but the system must check its expiry time.
- Adding subclasses is easy, but too many special pricing conditions may eventually require a separate pricing-rule strategy.

## Final Conclusion

The original design is modified rather than completely redesigned. The revised system uses inheritance, abstraction, polymorphism, interfaces and jagged arrays to support the new requirements. It maintains most of the existing tested classes while allowing future vehicle categories and pricing rules to be added with fewer changes.

Therefore, the modified design is more flexible, maintainable and scalable for the expanded university parking facility.
