# Line-by-line Walkthrough of `main.py`

Line numbers match the final `main.py` (480 lines). The file has five sections: data, helper functions, the menu, the features, and the main loop.

**Reading order tip:** read Section 1, then `main()` (line 444), then follow each menu option into its function.

**Python used:** only what the PRD lists: `print()`, `input()`, variables, strings, integers, Booleans, lists, dictionaries, a tuple, a set, `if/elif/else`, nested conditions, `for`, nested `for`, `for-else`, `break`, `continue`, `enumerate()`, `while`, plus a few simple string methods (`strip`, `upper`, `lower`, `isdecimal`).

---

## Header and Section 1: Data (lines 1-77)

| Lines | What it does and why |
|---|---|
| 1-6 | Comment block: name of the program and a reminder that data is in memory only. |
| 15 | `SUPPORTED_CITIES` is a **tuple** (round brackets). A tuple cannot be changed, which suits a fixed list of cities. The capital letters show it is a constant. |
| 19-67 | `routes` is a **list** `[ ... ]` of five **dictionaries** `{ ... }`. Each dictionary stores one route with keys such as `"route_id"` and `"ticket_price"`. `available_seats` starts equal to `total_seats`. Fares are whole numbers (integers). |
| 69 | `bookings = []` is an empty list. New booking dictionaries are appended here. Cancelled ones stay, which keeps history. |
| 73 | `issued_booking_ids = set()` is an empty **set**. A set holds each value once and quickly answers "is this ID in here?". |
| 75-77 | Comment: the functions only change what is inside these collections (append, add, edit a value). They never assign a brand-new value to the name, so `global` is not needed. |

---

## Section 2: Helper functions (lines 84-131)

### `find_route(route_id)` - lines 84-94

| Line | Explanation |
|---|---|
| 84 | Defines the function. It receives the ID to look for. |
| 86 | `for route in routes:` visits each route dictionary in turn. |
| 87 | Compares the stored ID with the wanted ID. `route["route_id"]` reads the value stored under that key. |
| 88 | Match: remember this route in `found_route`. |
| 89 | `break` stops the loop at once. There is no need to keep looking. |
| 90-93 | `else:` lined up with `for` is a **for-else**. It runs only if the loop ended **without** `break`, which means "no route matched". It sets `found_route = None`. |
| 94 | Returns the route dictionary, or `None` if nothing was found. Callers check `is None`. |

### `generate_booking_id()` - lines 97-105

| Line | Explanation |
|---|---|
| 99 | `number` starts at 1001. |
| 100 | `"B" + str(number)` builds the ID text `"B1001"`. `str()` turns the number into text so it can be joined. |
| 101-103 | `while new_id in issued_booking_ids:` is the uniqueness check (rule BR-06). If the ID was already issued, add 1 and try the next one, until an unused ID is found. |
| 104 | `.add()` puts the new ID into the set so it can never be issued again. |
| 105 | Returns the new ID. |

This function runs only after every booking check has passed, so a failed booking never uses up an ID.

### `is_positive_whole_number(text)` - lines 108-116

| Line | Explanation |
|---|---|
| 112-113 | `text.isdecimal()` is `True` only when the text is made entirely of digits. `"abc"`, `"2.5"`, `"-3"` and `""` all give `False`, so the function returns `False` before `int()` is called. This is how invalid input is handled safely without a crash. |
| 114-115 | A number that is zero is also invalid. |
| 116 | Otherwise return `True`. |

It is used for age (line 264) and seat quantity (line 279).

### `describe_journey(route)` - lines 119-121

Returns text such as `"Karachi -> Hyderabad"` by joining the two city values with ` -> `. It is reused wherever a journey is printed.

### `print_route_line(route)` - lines 124-131

Prints a one-line summary used by search, for example `R101 | Karachi -> Hyderabad | Rs. 1200 | 40 seats available`. If no seats are left (line 126) it prints `0 seats available (Fully Booked)` instead. `print()` with commas puts a space between the items and shows numbers without needing `str()`.

---

## Section 3: Menu (lines 138-150)

`show_menu()` only prints. `print()` with nothing inside prints a blank line. `"=" * 32` repeats the `=` character 32 times, which draws the divider. Options 1 to 8 match the PRD exactly.

---

## Section 4: Features

### Option 1 - `show_routes(only_available)` - lines 159-179

| Line | Explanation |
|---|---|
| 159 | The parameter `only_available` is a **Boolean** (`True` or `False`). `True` hides fully booked routes. |
| 163 | Prints the table header. Columns are separated by ` | `. |
| 165 | `shown_count` counts how many rows were actually printed. |
| 167 | `enumerate(routes, start=1)` gives two things per loop: a counter (1, 2, 3 ...) and the route. This is how each row gets its "No." |
| 168-169 | If the filter is on **and** the route has zero seats, `continue` skips the rest of this pass and goes to the next route. |
| 171 | Adds 1 to the counter (reached only by routes that were not skipped). |
| 172-174 | Builds the seats text, for example `40/40`. If the route is full, adds the words `Fully Booked`. |
| 175-176 | Prints one row: number, route ID, journey, departure time, fare, seats. |
| 178-179 | If nothing was shown (every route full with the filter on), prints a message. |

Because the numbers come from the full list, a route keeps the same "No." in both views (R104 is always No. 4).

### Option 1 - `show_schedule()` - lines 182-196

| Line | Explanation |
|---|---|
| 186 | **Outer loop**: goes through each city in the tuple. |
| 187 | Prints the city name as a heading. |
| 188 | `has_departure = False` is a Boolean flag: "have we printed any route for this city yet?". |
| 190 | **Inner loop**: goes through all routes for the current city. |
| 191-194 | If the route departs from the current city, print it and set the flag to `True`. |
| 195-196 | After the inner loop, if the flag is still `False`, print `No scheduled departures.` |

### Option 1 - `show_routes_menu()` - lines 199-214

Prints a three-choice sub-menu and reads the answer. `if/elif/else` calls `show_routes(False)`, `show_routes(True)` or `show_schedule()`. Any other text prints a message. The PRD has no menu slot for the schedule, so it lives in this sub-menu.

### Option 2 - `search_routes()` - lines 220-247

| Line | Explanation |
|---|---|
| 221-224 | Reads the search text. If it is empty, show a message and `return` (leave the function). |
| 226 | `term.upper()` is used for route-ID matching, because IDs are stored in capitals (`r101` becomes `R101`). |
| 227 | `term.lower()` is used for city matching. Line 242 lowers the stored cities too, so upper/lower case never matters. |
| 231-234 | **Step 1**: loop over routes. If an ID matches, print that route and `break`. IDs are unique, so one match is enough. |
| 235 | `else:` belongs to the `for` on line 231 (a **for-else**). It runs only when the loop finished with **no** `break`, meaning no route ID matched. |
| 240 | `match_count` counts city matches. |
| 241-245 | **Step 2**: loop over all routes and print every route that departs from or arrives in that city. There is no `break` here, because several routes can match. |
| 246-247 | If the count is still 0, print `No matching routes found.` |

### Option 3 - `book_ticket()` - lines 252-311

| Lines | Explanation |
|---|---|
| 256-257 | Plan: check each input first. The moment one is wrong, print a message and `return`. Nothing has been changed yet, so a failed booking leaves the data untouched. |
| 258-261 | Reads the name and removes extra spaces. An empty or spaces-only name becomes `""` after `.strip()` and is rejected (BR-01). |
| 263-267 | Reads the age as text and checks it with `is_positive_whole_number` (BR-02). Only after it passes is it turned into a number with `int()`. |
| 269-270 | Reads the route ID, makes it capitals, and looks it up with `find_route`. |
| 273-274 | If `route is None`, the route does not exist (BR-03). |
| 275-276 | If the route has zero seats, reject without asking for a quantity. |
| 277-280 | Otherwise ask for the number of seats and check it the same way as age (BR-04). |
| 281-282 | Valid seats: convert to a number. |
| 283-285 | **Nested condition**: only inside the valid-seats branch, check that seats are not more than the available seats (BR-05). |
| 286-288 | All checks passed. Calculate the fare: `price * seats` (BR-07). |
| 289 | Get a unique booking ID. |
| 291-299 | Build the booking dictionary with status `"Confirmed"`. |
| 300 | `append` adds the booking to the list. This is step 1 of the update. |
| 301 | Subtract the seats from the route. This is step 2, and it happens exactly once (BR-08). |
| 303-311 | Print the confirmation in the PRD format. |

The nesting runs: route exists, then seat entry is valid, then enough seats. Each `else` means "everything so far is fine".

### Option 4 - `view_bookings()` - lines 316-330

| Line | Explanation |
|---|---|
| 319-321 | If the list is empty, print `No bookings found.` and return (BR-15). |
| 323 | Table header. |
| 325 | `enumerate(bookings, start=1)` numbers each booking. |
| 326 | Looks up the booking's route so its journey can be shown. |
| 327-330 | Prints one row with ID, passenger, route, seats, fare and status. Cancelled bookings are included, because nothing is ever deleted (BR-11). |

### Option 5 - `cancel_booking()` - lines 335-358

| Line | Explanation |
|---|---|
| 338 | Reads the booking ID and makes it capitals so `b1001` also works. |
| 341-342 | Loop through the bookings looking for this ID. |
| 343 | Only a booking whose status is `"Confirmed"` may be cancelled (BR-09). |
| 345 | Finds the booking's original route. |
| 346 | Adds the booking's seats back to the route. |
| 347 | Changes the status to `"Cancelled"`. Lines 346 and 347 sit together so the seat change and the status change always happen as one operation. |
| 349-351 | Prints the result in the PRD format. |
| 352-354 | If the status is already `"Cancelled"`, print a message and do nothing. Because the status check blocks it, seats can never be restored twice (BR-10). |
| 355 | `break` stops the loop once the booking has been found and handled. |
| 356-358 | **for-else**: runs only if the loop ended without `break`, meaning the ID does not exist. Nothing is changed. |

### Option 6 - `show_availability()` - lines 363-397

| Line | Explanation |
|---|---|
| 364-376 | Prints a two-choice menu and sets the Boolean `only_available`. Any other answer prints a message and returns. |
| 378 | Table header. |
| 380-382 | Loop over routes. With the filter on, `continue` skips full routes. |
| 385-390 | Decides the status: all seats left = `Available`; some left = `Partially Booked`; none left = `Fully Booked`. The "all seats" test comes first, so the order of the checks matters. |
| 392-394 | Counts and prints one row: route, journey, available seats, total seats, status. |
| 396-397 | If nothing was printed, show a message. |

### Option 7 - `generate_report()` - lines 402-437

| Line | Explanation |
|---|---|
| 404 | `len(bookings)` counts every record, including cancelled ones. |
| 407-413 | One loop counts Confirmed and Cancelled records. |
| 417-419 | Totals start at zero. With no bookings they simply stay zero (BR-15). |
| 420 | **Outer loop**: one pass per route. |
| 421-424 | **Inner loop**: look at every booking. If it belongs to this route **and** is Confirmed, add its seats and fare. Cancelled bookings fail the `and` test, so they are never counted (BR-12). |
| 426-428 | A route with zero available seats counts as fully booked. This uses route data, so it works even with no bookings. |
| 430-437 | Prints the report. |

---

## Section 5: Main loop (lines 444-480)

| Line | Explanation |
|---|---|
| 446 | `while True:` repeats until `break` runs. |
| 447 | Shows the menu at the start of every pass (at startup and after every action). |
| 448 | Reads the choice as text. |
| 452-454 | `isdecimal()` is `False` for text such as `abc`, `3.5`, `-1` or an empty answer. A message is printed and `continue` jumps back to the top of the loop, which shows the menu again. |
| 456 | The text is only digits at this point, so it is safe to turn it into a number. |
| 458-471 | `if/elif` chain: each number from 1 to 7 calls one feature function. |
| 472-474 | Choice 8 prints a goodbye message and `break` leaves the loop. |
| 475-476 | Any other number (0, 99) prints an "Unsupported choice" message and the loop continues (BR-14). |
| 480 | `main()` starts the program. |

---

## Where each PRD concept appears

| Concept | Where |
|---|---|
| `print()` / `input()` | everywhere; for example lines 138-150 and 448 |
| Variables, strings, integers, Booleans | `only_available`, `has_departure`, IDs, seats, fares |
| List / dictionary | `routes`, `bookings`, each route and booking |
| Tuple | `SUPPORTED_CITIES` (line 15), used in `show_schedule` |
| Set | `issued_booking_ids` (line 73), used in `generate_booking_id` |
| `if / elif / else` | menu choice (458-476), status text (385-390) |
| Nested conditions | `book_ticket` (273-311), `cancel_booking` (342-355) |
| `for` loop | search, listings, report |
| Nested `for` loops | `show_schedule` (186-194), `generate_report` (420-428) |
| `for-else` | `find_route` (86-93), `search_routes` (231-247), `cancel_booking` (341-358) |
| `break` | lines 89, 234, 355, 474 |
| `continue` | lines 169, 382, 454 |
| `enumerate()` | lines 167, 325 |
| `while` | lines 101, 446 |
| Fare calculation | line 288 |
| Input validation | `is_positive_whole_number`, `book_ticket`, `main` |
