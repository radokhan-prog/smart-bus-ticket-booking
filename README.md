# Smart Bus Ticket Booking & Reservation System

A text-based Python 3 application for a small bus service. From a console menu you can browse routes, search, book seats, view bookings, cancel, check seat availability and generate a report.

This is a learning project built from the Product Requirements Document (PRD). It uses built-in Python only.

## Requirements

- Python 3 (any recent version).
- No external libraries.

## How to run

**From a terminal**

```
python main.py
```

**In Jupyter**

- Option A: run `%run main.py` in a cell, or
- Option B: paste the whole content of `main.py` into one cell and run it.

The menu appears right under the cell and each `input()` prompt shows a text box. Type your answer and press Enter. Choose option 8 to finish; the cell stops running when you exit. If you run the cell again, you get a fresh start with new data.

## Menu guide

| Option | What it does |
|---|---|
| 1. View Available Routes | Opens a small sub-menu: (1) all routes, (2) only routes with seats left, (3) route schedule grouped by departure city. |
| 2. Search for a Route | Type a route ID (for example `R101`) or a city. City search is not case-sensitive and finds routes that depart from or arrive at that city. |
| 3. Book a Ticket | Asks for passenger name, age, route ID and number of seats. Everything is validated before any data changes. Shows a confirmation with a booking ID. |
| 4. View My Bookings | Lists every booking, including cancelled ones, with its status. |
| 5. Cancel a Booking | Cancels a Confirmed booking by ID and gives its seats back to the route (once). The record stays in the history. |
| 6. View Seat Availability | Shows available seats versus total capacity and a status (Available, Partially Booked, Fully Booked). Optional filter for routes with seats left. |
| 7. Generate Booking Report | Shows booking counts, seats currently reserved, confirmed revenue and fully booked routes. |
| 8. Exit | Ends the program. |

Invalid menu text or numbers show a helpful message and the menu appears again.

## Sample workflow

1. Choose `1`, then `1` to see all five routes.
2. Choose `3` and enter: `Ahmed`, `24`, `R101`, `2`. The program confirms booking `B1001` for Rs. 2400, and R101 drops from 40 to 38 seats.
3. Choose `5` and enter `B1001`. The booking becomes Cancelled and R101 returns to 40 seats.
4. Choose `4` to see that the cancelled booking is still listed.
5. Choose `7` to see the report: cancelled bookings are not counted in revenue.
6. Choose `8` to exit.

## Business rules the program follows

- A booking needs a non-blank name, a positive whole-number age, an existing route ID and a positive whole number of seats that is not more than the seats available.
- Total fare = ticket price x seats.
- Seats are subtracted only after every check passes, and a failed check changes nothing.
- Booking IDs are unique (B1001, B1002, ...), checked against a set of issued IDs.
- Only a Confirmed booking can be cancelled, and its seats are restored exactly once.
- Cancelled bookings are kept for history but excluded from reserved seats and confirmed revenue.

## Data structures used

- **List of dictionaries** for routes and bookings.
- **Tuple** for the supported cities.
- **Set** for issued booking IDs.

## Storage limitation (please read)

All data is kept **in memory only**. Bookings are **not saved** anywhere. When the program ends (or the Jupyter cell is re-run), every booking is lost and the routes return to their starting seat counts. Persistence is outside the scope of this project.

## Constraints

- Console only: no GUI, website, database, payment, third-party API or external library.
- Single user: no login or staff accounts.
- Route data is fixed sample data (five fictional routes, fares in whole rupees).
- Optional enhancements from the PRD (discounts, fare categories, seat numbers and so on) are not included.

## Files

- `main.py` - the application
- `README.md` - this file
- `test_cases.txt` - the 12 required test cases with real results
