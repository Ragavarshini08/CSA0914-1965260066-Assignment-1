# TASK 3 – Design the Control Logic

**Bloom’s Level:** K4 – Analyse

## 1. Control Logic for Allocating a Free Slot

The allocation operation searches for an available parking slot floor by floor and slot by slot.

- The outer loop iterates through each floor.
- The inner loop checks every slot on that floor.
- If a slot contains null, it is considered free.
- The vehicle number is stored in that slot, and its floor and slot positions are returned.
- The search stops immediately after finding the first free slot.
- If all slots are occupied, the method returns null.

## 2. Pseudocode for Slot Allocation

**Algorithm: ALLOCATE_SLOT**

**Input:** Vehicle number

**Output:** Floor and slot position, or NULL.

```text
BEGIN

1. FOR each floor f in slots

2.   FOR each slot s in slots[f]

3.      IF slots[f][s] == NULL THEN

4.         slots[f][s] = vehicleNo

5.         RETURN {f, s}

6.      END IF

7.   END FOR

8. END FOR

9. RETURN NULL

END
```

## 3. Control Logic for Computing Parking Fee

The parking fee is calculated based on the total parking duration.

**Example fee rules:**

- First hour: ₹20.
- Each additional hour: ₹10.
- Daily maximum charge: ₹100.

The duration is converted into billable hours. The if-else statements are used to apply the correct fee slab.

## 4. Pseudocode for Parking Fee Calculation

**Algorithm: CALCULATE_FEE**

**Input:** Parking duration in minutes.

**Output:** Total parking fee.

```text
BEGIN

1. READ duration

2. hours = CEILING(duration / 60.0)

3. IF hours <= 1 THEN

      fee = 20

   ELSE

      fee = 20 + (hours - 1) * 10

   END IF

4. IF fee > 100 THEN

      fee = 100

   END IF

5. RETURN fee

END
```

**Example:**

If a vehicle is parked for 150 minutes:

- Billable hours = 3.
- First hour charge = ₹20.
- Additional two hours = ₹20.
- Total fee = ₹40.

The daily maximum ensures that the parking fee does not exceed ₹100.

## 5. Usage of break, continue and return

1. **break:** Stops the current loop when a free parking slot is found, avoiding unnecessary searching.

2. **continue:** Skips the current iteration when a slot is occupied and proceeds to the next slot.

3. **return:** Immediately exits the method after allocating a slot or when no free slot is available.

If no suitable parking slot is free, the allocation method returns null, indicating that the parking facility is full.

## 6. Complete Java Program

The following program completes the allocate() and release() methods without changing the given main() method.

```java
public class ParkingFacility {

    private final String[][] slots;

    public ParkingFacility(int[] slotsPerFloor) {

        slots = new String[slotsPerFloor.length][];

        for (int f = 0; f < slotsPerFloor.length; f++) {

            slots[f] = new String[slotsPerFloor[f]];

        }

    }

    public int[] allocate(String vehicleNo) {

        for (int f = 0; f < slots.length; f++) {

            for (int s = 0; s < slots[f].length; s++) {

                if (slots[f][s] == null) {

                    slots[f][s] = vehicleNo;

                    return new int[]{f, s};

                }

            }

        }

        return null;

    }

    public boolean release(int floor, int slot) {

        if (floor < 0 || floor >= slots.length) {

            return false;

        }

        if (slot < 0 || slot >= slots[floor].length) {

            return false;

        }

        if (slots[floor][slot] == null) {

            return false;

        }

        slots[floor][slot] = null;

        return true;

    }

    public static void main(String[] args) {

        ParkingFacility p = new ParkingFacility(new int[]{2, 1});

        System.out.println(java.util.Arrays.toString(p.allocate("TN01AB1111")));

        System.out.println(java.util.Arrays.toString(p.allocate("TN02CD2222")));

        System.out.println(java.util.Arrays.toString(p.allocate("TN03EF3333")));

        System.out.println(java.util.Arrays.toString(p.allocate("TN04GH4444")));

        System.out.println(p.release(0, 1) + " " + p.release(0, 1) + " "
                + p.release(5, 0));

        System.out.println(java.util.Arrays.toString(p.allocate("TN04GH4444")));

    }

}
```

### Output

```text
[0, 0]
[0, 1]
[1, 0]
null
true false false
[0, 1]
```

## 7. Alternative Concept and Complexity Analysis

Instead of searching every slot, we can maintain a count of free slots on each floor and use a stack to store the free slot numbers.

### Allocation Using a Stack

1. Check the free slot count of each floor.
2. Select a floor that has available slots.
3. Pop a free slot number from its stack.
4. Allocate that slot to the vehicle.
5. Decrease the free slot count by one.

### Release Using a Stack

1. Validate the floor and slot numbers.
2. Check whether the slot is occupied.
3. Push the released slot number into the corresponding floor's stack.
4. Increase the free slot count by one.

### Time Complexity

**Existing Approach:**

- allocate(): O(F × S) in the worst case because all floors and slots may need to be searched.
- release(): O(1) because the floor and slot are accessed directly.

**Stack-Based Approach:**

- allocate(): O(1) if a non-empty floor can be selected directly using an additional stack or index structure.
- release(): O(1) because the released slot is pushed directly into the stack.

### Space Complexity

- **Existing Approach:** O(F × S) space for storing all parking slots. No additional stack is required.
- **Stack-Based Approach:** O(F × S) space for parking slots and free-slot stacks, with additional O(F) space for maintaining free-slot counts.

## 8. Conclusion

The nested-loop approach is simple and suitable for small parking facilities. A stack-based approach improves allocation speed by avoiding repeated searches. Therefore, the stack-based approach is more efficient for large parking facilities with frequent vehicle entry and exit operations.
