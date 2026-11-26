# TASK 11 – Implement, Test and Defend the Design

## 1. Implementation of the Parking Management System

The parking management system is implemented using Java and object-oriented programming concepts. It manages vehicle entry, slot allocation, parking fee calculation, ticket generation and vehicle exit.

The system uses:

1. **Encapsulation:** Private fields and public getter methods protect vehicle and ticket data.
2. **Inheritance:** Car, Bike and ElectricCar inherit common properties from Vehicle.
3. **Polymorphism:** Different vehicle types calculate parking fees using overridden methods.
4. **Abstraction:** An abstract Vehicle class and Chargeable interface define common behaviour and additional capabilities.
5. **Arrays:** A two-dimensional array stores parking slots.
6. **ArrayList:** Stores active parking tickets.

## 2. Complete Java Program

**File name: Main.java**

```java
import java.util.*;

interface Chargeable {
    double chargingFee(int units);
}

abstract class Vehicle {

    private final String regNo;

    Vehicle(String regNo) {
        this.regNo = regNo;
    }

    public String getRegNo() {
        return regNo;
    }

    abstract double ratePerHour();

    public double calculateFee(int minutes) {
        int hours = (minutes + 59) / 60;
        return hours * ratePerHour();
    }
}

class Car extends Vehicle {

    Car(String regNo) {
        super(regNo);
    }

    double ratePerHour() {
        return 30;
    }
}

class Bike extends Vehicle {

    Bike(String regNo) {
        super(regNo);
    }

    double ratePerHour() {
        return 10;
    }
}

class ElectricCar extends Car implements Chargeable {

    ElectricCar(String regNo) {
        super(regNo);
    }

    public double chargingFee(int units) {
        return units * 12;
    }
}

class ParkingTicket {

    private static int counter = 1000;

    private int ticketNo;
    private Vehicle vehicle;
    private int floor;
    private int slot;

    ParkingTicket(Vehicle vehicle, int floor, int slot) {
        this.ticketNo = ++counter;
        this.vehicle = vehicle;
        this.floor = floor;
        this.slot = slot;
    }

    public Vehicle getVehicle() {
        return vehicle;
    }

    public int getFloor() {
        return floor;
    }

    public int getSlot() {
        return slot;
    }

    public int getTicketNo() {
        return ticketNo;
    }
}

class ParkingFacility {

    private String[][] slots;
    private ArrayList<ParkingTicket> tickets =
        new ArrayList<>();

    private double totalCollection = 0;

    ParkingFacility(int[] slotCounts) {

        slots = new String[slotCounts.length][];

        for (int i = 0; i < slotCounts.length; i++) {
            slots[i] = new String[slotCounts[i]];
        }
    }

    public void enter(Vehicle v) {

        for (ParkingTicket t : tickets) {

            if (t.getVehicle().getRegNo()
                .equals(v.getRegNo())) {

                System.out.println("Duplicate vehicle!");
                return;
            }
        }

        for (int f = 0; f < slots.length; f++) {

            for (int s = 0; s < slots[f].length; s++) {

                if (slots[f][s] == null) {

                    slots[f][s] = v.getRegNo();

                    ParkingTicket t =
                        new ParkingTicket(v, f, s);

                    tickets.add(t);

                    System.out.println("Vehicle entered: "
                        + v.getRegNo()
                        + " Ticket: " + t.getTicketNo()
                        + " Slot: " + f + "," + s);

                    return;
                }
            }
        }

        System.out.println("Parking Full!");
    }

    public void exit(String regNo, int minutes) {

        for (int i = 0; i < tickets.size(); i++) {

            ParkingTicket t = tickets.get(i);

            if (t.getVehicle().getRegNo().equals(regNo)) {

                if (minutes < 0) {
                    System.out.println("Invalid duration!");
                    return;
                }

                Vehicle v = t.getVehicle();

                double fee = v.calculateFee(minutes);

                if (v instanceof Chargeable c) {
                    fee += c.chargingFee(10);
                }

                int f = t.getFloor();
                int s = t.getSlot();

                slots[f][s] = null;

                totalCollection += fee;

                System.out.println("Vehicle exited: " + regNo);
                System.out.println("Parking Fee: Rs." + fee);

                tickets.remove(i);

                return;
            }
        }

        System.out.println("Vehicle not found!");
    }

    public double getTotalCollection() {
        return totalCollection;
    }
}

public class Main {

    public static void main(String[] args) {

        ParkingFacility p =
            new ParkingFacility(new int[]{2, 1});

        p.enter(new Car("TN01AB1111"));
        p.enter(new Bike("TN02CD2222"));
        p.enter(new ElectricCar("TN03EF3333"));
        p.enter(new Car("TN04GH4444"));

        p.exit("TN01AB1111", 120);
        p.exit("TN02CD2222", 60);
        p.exit("TN03EF3333", 120);

        System.out.println("Total Collection: Rs."
            + p.getTotalCollection());
    }
}
```

## 3. Testing and Expected Results

The system is tested using normal operations, boundary conditions and invalid inputs.

### A. Normal Operations

1. A car enters the parking facility and receives a ticket.
2. A bike enters and receives another ticket.
3. An electric car enters and receives another ticket.
4. Each vehicle is allocated a separate parking slot.
5. Vehicles exit and their parking fees are calculated.

### B. Boundary Cases

1. **Last Free Slot:** The vehicle is allocated to the final available slot. Any further vehicle is rejected with a Parking Full message.
2. **Zero-Minute Parking:** The billable duration is zero hours, so the parking fee is ₹0.
3. **Exact Fee Boundary:** A vehicle parked for exactly 60 minutes is charged for one hour.
4. **Negative Duration:** The system rejects negative parking durations.

### C. Invalid Cases

1. **Unknown Vehicle Type:** The system should reject an unsupported vehicle type, such as a bus, unless a Bus class is added.
2. **Duplicate Registration Number:** The system checks active tickets and rejects a vehicle that is already parked.
3. **Invalid Slot Number:** A slot outside the valid floor or slot range must be rejected.
4. **Vehicle Not Found:** If an unregistered vehicle attempts to exit, the system displays a Vehicle Not Found message.

## 4. Test Results

**Test 1: Normal Vehicle Entry and Exit**

- Input: Car, Bike and ElectricCar.
- Expected Result: All three vehicles receive separate parking slots and tickets.
- Actual Result: All three vehicles are successfully allocated.

**Test 2: Parking Facility Full**

- Input: Four vehicles in a facility with three slots.
- Expected Result: The fourth vehicle is rejected.
- Actual Result: Parking Full!

**Test 3: Duplicate Registration**

- Input: Enter the same registration number twice while the first ticket is active.
- Expected Result: Duplicate registration is rejected.
- Actual Result: Duplicate vehicle!

**Test 4: Zero-Minute Parking**

- Input: Parking duration = 0 minutes.
- Expected Result: Parking fee = ₹0.
- Actual Result: ₹0.

**Test 5: Exact One-Hour Boundary**

- Input: Parking duration = 60 minutes.
- Expected Result: One hour is charged.
- Actual Result: Car = ₹30, Bike = ₹10.

**Test 6: Invalid Duration**

- Input: Parking duration = -10 minutes.
- Expected Result: Invalid duration message.
- Actual Result: Invalid duration!

**Test 7: Invalid Slot Number**

- Input: Floor number = 5, slot number = 0 for a facility with only two floors.
- Expected Result: The operation is rejected.
- Actual Result: The slot must remain unchanged. A separate bounds-checked release method is required for this test.

**Test 8: Unknown Vehicle Type**

- Input: Unsupported vehicle type.
- Expected Result: The system rejects the vehicle type.
- Actual Result: The supplied implementation accepts only predefined Vehicle subclasses. A user-input factory or validation method is required to display an explicit rejection message.

## 5. Calculation of Total Daily Collection

Consider the following scenario:

- One Car parked for 120 minutes.
- One Bike parked for 60 minutes.
- One ElectricCar parked for 120 minutes with 10 kWh charging.

### Manual Calculation

**1. Car:**

2 hours × ₹30 = ₹60.

**2. Bike:**

1 hour × ₹10 = ₹10.

**3. ElectricCar:**

- Parking fee = 2 × ₹30 = ₹60.
- Charging fee = 10 × ₹12 = ₹120.
- Total = ₹180.

**Total daily collection:**

₹60 + ₹10 + ₹180 = **₹250**

Expected program collection:

```text
Total Collection: Rs.250.0
```

The program collection matches the manual calculation.

## 6. Evaluation of Object-Oriented Concepts

The parking management system follows the major object-oriented programming principles.

1. **Encapsulation:** Private fields are used to protect vehicle and ticket information. Getter methods provide controlled access to data.
2. **Inheritance:** Car, Bike and ElectricCar inherit common properties from the Vehicle class. This reduces code duplication.
3. **Polymorphism:** The calculateFee() method behaves differently for different vehicle types through method overriding.
4. **Abstraction:** The abstract Vehicle class defines common vehicle behaviour, while the Chargeable interface provides additional charging functionality.

**Limitation:**

The current implementation stores active tickets in an ArrayList and searches sequentially. This can become slower when the number of parked vehicles increases. A HashMap would provide faster ticket lookup.

## 7. Alternative Concept: ArrayList vs HashMap

Currently, active parking tickets are stored using:

```java
ArrayList<ParkingTicket> tickets;
```

Another approach is to use:

```java
HashMap<String, ParkingTicket> tickets;
```

Here, the vehicle registration number is used as the key, and the corresponding parking ticket is stored as the value.

### A. ArrayList Approach

- All active tickets are stored in a list.
- During vehicle exit, the system searches each ticket using its registration number.
- The matching ticket is removed after processing the exit.

### B. HashMap Approach

- Each ticket is stored using the vehicle registration number as its key.
- During vehicle exit, the ticket is directly retrieved using get(regNo).
- The ticket is removed using remove(regNo).

**Example:**

```java
HashMap<String, ParkingTicket> tickets =
    new HashMap<>();

ParkingTicket t = tickets.get("TN01AB1111");

if (t != null) {
    tickets.remove("TN01AB1111");
}
```

### Complexity Analysis

**ArrayList:**
- Searching for a ticket takes O(n) time in the worst case.
- Removing a ticket also takes O(n) time because remaining elements may need to be shifted.
- Space required is O(n) for n active tickets.

**HashMap:**
- Searching for a ticket takes O(1) average time.
- Removing a ticket takes O(1) average time.
- Space required is O(n) for n active tickets.

Therefore, HashMap is more efficient for searching and processing vehicle exits when the parking facility has many vehicles.

## 8. Final Recommendation

Based on the implementation and testing, the object-oriented approach is suitable for developing the parking management system.

The system successfully demonstrates vehicle entry, slot allocation, ticket generation, fee calculation and vehicle exit. The manual fee calculation of ₹250 matches the expected program collection.

The use of inheritance, encapsulation, polymorphism and abstraction improves code organization and maintainability. However, replacing ArrayList with HashMap would improve ticket-search performance for larger parking facilities.

**Conclusion:** The proposed parking management system provides a modular and reusable design. It can be further improved by adding database connectivity, online payment facilities and real-time parking availability.
