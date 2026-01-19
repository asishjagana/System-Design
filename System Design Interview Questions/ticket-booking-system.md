# Design a Ticket Booking System (Ticketmaster / IRCTC)

How to design a system that handles high-concurrency ticket sales without overbooking.

## 1. Requirements
- **Search**: Find events/trains.
- **Booking**: Reserve a seat for a fixed time (e.g., 10 minutes).
- **Payment**: Complete transaction safely.
- **No Overbooking**: Never sell the same seat twice.

## 2. The Core Challenge: The "Flash Sale"
When Taylor Swift tickets go on sale, millions try to book the same few thousand seats simultaneously.

## 3. Database Strategy: ACID Transactions
We must use a Relational Database (SQL) because we need **Atomicity** and **Isolation**.
```sql
BEGIN TRANSACTION;
  -- Check availability
  SELECT status FROM seats WHERE seat_id = 123 AND status = 'AVAILABLE' FOR UPDATE;
  
  -- Update if available
  UPDATE seats SET status = 'RESERVED', user_id = 'user1' WHERE seat_id = 123;
COMMIT;
```
*The `FOR UPDATE` creates a row-level lock, preventing other users from reading/writing that specific seat until the transaction finishes.*

## 4. Handling High Volume (Waiting Rooms)
Instead of letting millions of requests hit the DB, use a **Virtual Waiting Room**.
1. Users enter a **Message Queue** (like Kafka).
2. A service slowly lets a fixed number of users into the "Booking Page" per minute.

## 5. High-Level Architecture
1. **Event Service**: Metadata about shows.
2. **Search Service**: ElasticSearch for fast searching.
3. **Booking Service**: Manages state (Available -> Reserved -> Booked).
4. **Payment Service**: Third-party integration (Stripe/Paypal).
5. **Worker**: Background job to release "Expired" reservations (if payment fails after 10 mins).

## 6. Pros and Cons
| Pros | Cons |
| :--- | :--- |
| **Data Integrity**: SQL locks ensure no double-booking. | **Database Bottleneck**: Locks can slow down the system during peak times. |
| **Fairness**: Waiting rooms ensure a first-come, first-served experience. | **Complexity**: Managing distributed transactions (Saga pattern) between Booking and Payment. |

## 7. Real-Time Use Case
**IRCTC** (Indian Railways) handles millions of bookings per minute during "Tatkal" hours. **Ticketmaster** uses advanced bot detection and queuing to stop scalpers.
