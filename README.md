Let me re-read the lab manual carefully before answering.The lab manual is already in our conversation context from earlier — I'll work from that. Here's everything you need.

---

# 🏨 Your Project Explained Simply

## What is it?
A **Hotel Management System** — a desktop application built in Java that lets hotel staff manage rooms, take bookings, check guests in and out, and view reports. It runs as a standalone window (JavaFX GUI), stores all data in a database (SQLite), and requires no internet or external server.

## How is it structured?
Think of it in 4 layers:

```
UI (JavaFX Controllers)  ←  what the user sees and clicks
        ↓
Model (Room, Booking)    ←  the data objects
        ↓
DAO (RoomDAO, BookingDAO) ← the database logic
        ↓
SQLite Database          ←  where data is permanently stored
```

## What can it do?
- **Dashboard** — shows live stats: total rooms, available, occupied, revenue, active bookings
- **Rooms** — add, edit, delete rooms; filter and search; see availability status
- **Bookings** — create a booking by picking a room, guest details, dates; auto-calculates total cost
- **Check-In/Out** — look up a booking by ID, confirm check-in, do checkout (room auto becomes available again)
- **Reports** — occupancy bars per room type, revenue pie chart, booking history summary

---

# 📚 OOP Concepts in Your Project (Mapped to Each Week)

This is the most important part for your oral exam. For every concept, know **where** it is in your code.

---

## Week 1 — OOP Concepts

**Encapsulation**
- `Room.java` and `Booking.java` have all fields `private` (like `roomNumber`, `guestName`)
- Access only through getter/setter methods — e.g. `getRoomNumber()`, `setGuestName()`
- JavaFX Properties like `SimpleIntegerProperty` are a modern form of encapsulation — they wrap primitive values and allow binding

**Inheritance**
- All controllers (`DashboardController`, `RoomController`, etc.) follow a common pattern — each has a `buildView()` method
- JavaFX itself uses inheritance heavily — `VBox`, `HBox`, `GridPane` all extend `Pane` which extends `Region` which extends `Node`

**Polymorphism**
- The `TableCell` factory uses runtime polymorphism — `updateItem()` is overridden differently for each column (Boolean status column shows a coloured dot, Double price column shows ₹ symbol)
- `setOnAction(e -> ...)` event handlers are polymorphic lambda implementations

**Abstraction**
- The DAO pattern abstracts database operations — `RoomController` just calls `roomDAO.getAllRooms()` and never knows or cares about SQL
- `DatabaseManager` hides connection management behind `getInstance()` and `getConnection()`

**Constructors**
- `Room` has a no-arg constructor and a full parameterised constructor
- `Booking` similarly has both — used when creating new bookings vs loading from DB

**this and super keywords**
- In `Room.java` setters: `this.roomNumber = roomNumber` distinguishes field from parameter
- In DAOs: `this.roomDAO = roomDAO` in constructors

---

## Week 2 — Wrapper Classes, Autoboxing, Enum

**Wrapper Classes**
- `Integer`, `Double`, `Boolean` are used throughout JavaFX properties — `SimpleIntegerProperty`, `SimpleDoubleProperty`, `SimpleBooleanProperty`
- In `BookingDAO`: `rs.getInt()` returns `int`, stored into `Integer` objects via autoboxing

**Autoboxing / Unboxing**
- When you do `int totalRooms = roomDAO.getTotalRooms()` and pass it to `String.valueOf(totalRooms)` — unboxing happens automatically
- `ObservableList<Integer>` stores Integer objects but you add plain `int` values — autoboxing

**Enum** — not explicitly declared but concept applies:
- Room types (Standard, Deluxe, Suite, Executive) would be better as an enum — you can mention this as an improvement you considered
- The `status` field in `Booking` ("ACTIVE", "CHECKED_OUT", "CANCELLED") is a candidate for enum

---

## Week 3 & 4 — Multithreading & Synchronization

**Threading**
- JavaFX runs on the **JavaFX Application Thread** — all UI updates happen on this thread
- The live clock in the sidebar uses `Timeline` with `KeyFrame` — this is JavaFX's built-in scheduling, similar to a thread that ticks every second
- `PauseTransition` in the dashboard animations runs asynchronously — it's non-blocking

**Synchronization concept**
- `DatabaseManager` uses the **Singleton pattern** — `getInstance()` ensures only one database connection exists, which is the same principle as preventing race conditions in multithreaded access

---

## Week 5 — I/O Streams

**File I/O**
- SQLite creates a physical file `hotel_management.db` on disk — this is file I/O at the database level
- `DatabaseManager` uses `FileInputStream` equivalent through JDBC — the driver reads/writes the `.db` file as a binary stream

---

## Week 6 — Random Access File, Serialization

**Random Access**
- SQLite's `seek()` equivalent is the SQL `WHERE room_number = ?` query — it jumps directly to a record without reading all rows sequentially, exactly like `RandomAccessFile.seek()`

**Serialization concept**
- Instead of Java object serialization (`ObjectOutputStream`), the project uses database persistence — which is the industry-standard equivalent. `Room` and `Booking` objects are serialized to rows and deserialized back to Java objects through the `mapResultSet()` method in each DAO

---

## Week 7 — Generics

**Generic classes and methods**
- `ObservableList<Room>`, `ObservableList<Booking>` — type-safe collections
- `TableView<Room>`, `TableColumn<Room, String>` — heavily generic JavaFX classes
- The `col()` helper method in `RoomController` is a **generic method**: `private <T> TableColumn<Room, T> col(...)` — it creates columns for any data type T
- `TableCell<Room, Boolean>` for status, `TableCell<Room, Double>` for price — bounded generics

---

## Week 8 — Collections Framework

**ArrayList**
- `roomDAO.getAllRooms()` returns `List<Room>` backed by `ArrayList`
- Booking lists, filter results all use `ArrayList`

**ObservableList**
- `FXCollections.observableArrayList(...)` — a JavaFX-specific list that automatically updates the TableView when items change

**HashMap** concept
- `DatabaseManager` maps room numbers to booking records via SQL joins — equivalent to `HashMap<Integer, Booking>`

**Iterator**
- Used implicitly in every `for (Room r : rooms)` enhanced for-loop
- `stream().filter().toList()` for search/filter operations

**Collections.sort()**
- `bookingDAO.getAllBookings()` orders by `booking_id DESC` — equivalent to `Collections.sort()` with a comparator

---

## Week 9 & 10 — JavaFX GUI

**JavaFX Architecture**
- `Stage` → `Scene` → root `Node` (BorderPane) → all child nodes
- `MainApp` extends `Application`, overrides `start(Stage stage)`

**Controls used**
- `Label`, `TextField`, `Button`, `ComboBox`, `DatePicker`, `TableView`, `TableColumn`, `ScrollPane`, `Dialog`, `Alert`

**Layouts used**
- `BorderPane` — main layout (sidebar left, content center)
- `VBox`, `HBox` — vertical and horizontal stacking
- `GridPane` — dashboard stat cards, table rows, booking form
- `StackPane` — content area for page transitions

**Event Handling**
- `btn.setOnAction(e -> ...)` — lambda event handlers
- `textField.textProperty().addListener(...)` — property change listeners for live search
- `tableView.sceneProperty().addListener(...)` — lifecycle events

**Animations**
- `FadeTransition` — page fade-in when switching tabs
- `TranslateTransition` — cards slide up on dashboard load
- `ParallelTransition` — runs fade + slide simultaneously
- `PauseTransition` — staggered delay so cards animate one by one
- `Timeline` + `KeyFrame` — live clock updating every second

---

# ❓ Questions You'll Most Likely Get

## Basic / Definition Questions

**Q: What is encapsulation and where is it in your project?**
A: Encapsulation means binding data and methods together and hiding internal data. In my project, `Room.java` has all fields private — `roomNumber`, `roomType`, `pricePerNight` etc. — and they can only be accessed through public getter and setter methods like `getRoomNumber()` and `setPricePerNight()`. I also used JavaFX Properties which add another layer of encapsulation by wrapping primitive values.

**Q: What is polymorphism and where have you used it?**
A: Polymorphism means one method taking multiple forms. In my project, the `TableCell` factory demonstrates runtime polymorphism — the `updateItem()` method is overridden differently for each column. For example the status column overrides it to show a coloured circle, while the price column overrides it to format the value with a ₹ symbol. JavaFX resolves the correct version at runtime.

**Q: What is the DAO pattern?**
A: DAO stands for Data Access Object. It separates database logic from UI logic. `RoomDAO` handles all SQL for rooms — `getAllRooms()`, `addRoom()`, `updateRoom()`, `deleteRoom()`. The controllers just call these methods and never write SQL themselves. This makes the code modular and easy to maintain.

**Q: What is a Singleton and where is it used?**
A: A Singleton ensures only one instance of a class exists. `DatabaseManager` uses it — the constructor is private, and `getInstance()` returns the same object every time. This ensures only one database connection is open throughout the application.

**Q: What is JDBC?**
A: JDBC stands for Java Database Connectivity. It's an API that lets Java programs connect to databases using SQL. In my project I use it to connect to SQLite — `DriverManager.getConnection()` opens the connection, `PreparedStatement` executes parameterised queries safely, and `ResultSet` holds the returned rows which I map to Java objects.

---

## Code-Specific Questions

**Q: Why did you use SQLite instead of MySQL?**
A: SQLite is file-based — it requires no separate server installation. The database is just a `.db` file created automatically when the app runs. This makes the project truly standalone, which matches the lab requirement of no external database server.

**Q: What is `ObservableList` and why did you use it instead of `ArrayList`?**
A: `ObservableList` is a JavaFX collection that notifies the UI automatically when items are added or removed. When I do `tableView.setItems(observableList)`, the TableView watches that list — if I add a room to the list, the table updates instantly without me calling any refresh method manually.

**Q: What is a `PreparedStatement` and why use it over a regular `Statement`?**
A: `PreparedStatement` uses `?` placeholders for values and the JDBC driver safely escapes them. This prevents SQL injection — if someone types malicious SQL in the guest name field, it gets treated as plain text, not executed as code. A regular `Statement` with string concatenation would be vulnerable.

**Q: What does `mapResultSet()` do in your DAO classes?**
A: It converts a database row into a Java object. When `ResultSet` holds a row from the database, `mapResultSet()` reads each column — `rs.getInt("room_number")`, `rs.getString("room_type")` etc. — and creates a `Room` or `Booking` object. It's essentially deserialization from database to Java object.

**Q: Explain the generic method `col()` in RoomController.**
A: `private <T> TableColumn<Room, T> col(String name, String property, int width)` — the `<T>` makes it generic, so I can create columns for any data type. I call it as `col("Room #", "roomNumber", 80)` which gives `TableColumn<Room, Integer>`, and `col("Type", "roomType", 100)` which gives `TableColumn<Room, String>`. Without generics I would need a separate method for each data type.

**Q: How does checking out a room work end to end?**
A: When the user clicks checkout for a booking ID — `BookingDAO.checkoutBooking(id)` updates the booking's status to `CHECKED_OUT` in the database, then `RoomDAO.updateAvailability(roomNumber, true)` sets the room's `available` column back to 1. The next time rooms are loaded, that room appears as available again.

**Q: What JavaFX layout did you use for the main window and why?**
A: `BorderPane` — it has designated regions: left, right, top, bottom, center. The sidebar navigation goes in `left` and the page content goes in `center`. This is the standard layout for applications with a fixed navigation panel.

**Q: How do the page transitions work?**
A: When a nav button is clicked, `setContent(Node node)` is called. It sets the new node's opacity to 0, adds it to the `StackPane` content area, then runs a `FadeTransition` that animates opacity from 0 to 1 over 300 milliseconds. This gives a smooth fade-in effect between pages.

---

## "What would you improve?" Questions

**Q: What improvements would you make?**
A: 
- Replace the String-based room type and booking status with proper **enums** (Week 2 concept)
- Add **multithreading** — load data from the database on a background thread so the UI doesn't freeze on slow queries, using `Platform.runLater()` to update the UI safely
- Add **user authentication** — login screen with hashed passwords
- Generate a **PDF invoice** using file I/O when a guest checks out
- Add **input validation** with proper error highlighting on fields