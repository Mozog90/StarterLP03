# North Vacation Booking - Black-Box Testing, Part 1

## Step 1: Identify Test Classifications

The four classifications are number of nights, number of guests, check-in and checkout dates, and availability. I used equivalence partitioning to identify valid and invalid groups and chose a representative value for each group.

## Step 2: Equivalence Partition Tables

### Number of Nights

| Partition    | Range or Condition  | Test Value | Expected Result                                |
| ------------ | ------------------- | ---------- | ---------------------------------------------- |
| Invalid low  | Less than 1 night   | 0 nights   | `ValueError`                                   |
| Valid        | 1-31 nights         | 3 nights   | Reservation accepted if other inputs are valid |
| Invalid high | More than 31 nights | 32 nights  | `ValueError`                                   |

### Number of Guests

| Partition             | Range or Condition  | Test Value | Expected Result                         |
| --------------------- | ------------------- | ---------- | --------------------------------------- |
| Invalid low           | Less than 1 guest   | 0 guests   | `ValueError`                            |
| Valid, standard rate  | 1-5 guests          | 3 guests   | Accepted; no extra guest fee            |
| Valid, additional fee | 6-12 guests         | 6 guests   | Accepted; $25 per extra guest per night |
| Invalid high          | More than 12 guests | 13 guests  | `ValueError`                            |

### Check-in and Checkout Dates

| Partition               | Range or Condition       | Test Value             | Expected Result    |
| ----------------------- | ------------------------ | ---------------------- | ------------------ |
| Valid                   | Check-in before checkout | Nov 10 to Nov 13, 2026 | Accepted; 3 nights |
| Invalid, same date      | Check-in equals checkout | Nov 10 to Nov 10, 2026 | `ValueError`       |
| Invalid, reversed dates | Check-in after checkout  | Nov 13 to Nov 10, 2026 | `ValueError`       |

### Availability

| Partition | Range or Condition                         | Test Value                   | Expected Result    |
| --------- | ------------------------------------------ | ---------------------------- | ------------------ |
| Valid     | All reserved nights are available          | Nov 10, 11, and 12 available | Accepted           |
| Invalid   | At least one reserved night is unavailable | Nov 11 unavailable           | `UnavailableError` |

## Step 3: Test Cases

For these tests, the default valid reservation is November 10-13, 2026 (3 nights), for 3 guests, with all reserved nights available. Unless stated otherwise, the expected price for this reservation is $650: (3 x $200) + $50 cleaning fee. When a test changes one input, the other inputs are assumed valid.

| Test ID | Classification             | Partition                       | Input                                                   | Expected Result                                         |
| ------- | -------------------------- | ------------------------------- | ------------------------------------------------------- | ------------------------------------------------------- |
| TC01    | Number of nights           | Invalid low                     | Check-in Nov 10; checkout Nov 10 (0 nights)             | `ValueError`                                            |
| TC02    | Number of nights           | Valid                           | Check-in Nov 10; checkout Nov 13 (3 nights)             | Accepted; total $650                                    |
| TC03    | Number of nights           | Invalid high                    | Check-in Nov 1; checkout Dec 3 (32 nights)              | `ValueError`                                            |
| TC04    | Number of guests           | Invalid low                     | 0 guests; otherwise valid booking                       | `ValueError`                                            |
| TC05    | Number of guests           | Valid, standard rate            | 3 guests; 3 nights                                      | Accepted; total $650                                    |
| TC06    | Number of guests           | Valid, additional fee           | 6 guests; 3 nights                                      | Accepted; total $725 ($600 + $50 + $75 extra guest fee) |
| TC07    | Number of guests           | Invalid high                    | 13 guests; otherwise valid booking                      | `ValueError`                                            |
| TC08    | Dates                      | Valid                           | Check-in Nov 10; checkout Nov 13                        | Accepted; 3 nights; total $650                          |
| TC09    | Dates                      | Invalid, same date              | Check-in Nov 10; checkout Nov 10                        | `ValueError`                                            |
| TC10    | Dates                      | Invalid, reversed dates         | Check-in Nov 13; checkout Nov 10                        | `ValueError`                                            |
| TC11    | Availability               | Valid                           | Nov 10, 11, and 12 available                            | Accepted; total $650                                    |
| TC12    | Availability               | Invalid                         | Nov 11 unavailable during Nov 10-13 reservation         | `UnavailableError`                                      |
| TC13    | Availability (extra check) | Valid; checkout is not reserved | Nov 10-12 available; Nov 13 unavailable (checkout date) | Accepted; total $650                                    |

## How Equivalence Partitioning Helps

Equivalence partitioning reduces how many tests we need by grouping inputs that should have the same result. For example, instead of testing every guest count from 1 to 12, we test a value from 1-5 guests and a value from 6-12 guests because those groups have different pricing rules. We also test invalid values below and above the allowed range.

These are designed test cases based on the requirements. No application code was provided, so the tests have not been executed.

## Reference

GeeksforGeeks. (2026, July 3). _Equivalence partitioning method_. https://www.geeksforgeeks.org/software-engineering/equivalence-partitioning-method/
