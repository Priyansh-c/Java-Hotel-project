
---

# 🏨 Java Hotel Project

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

# 📚 OOP Concepts in My Project 


---



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

## Wrapper Classes, Autoboxing, Enum

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

## Multithreading & Synchronization

**Threading**
- JavaFX runs on the **JavaFX Application Thread** — all UI updates happen on this thread
- The live clock in the sidebar uses `Timeline` with `KeyFrame` — this is JavaFX's built-in scheduling, similar to a thread that ticks every second
- `PauseTransition` in the dashboard animations runs asynchronously — it's non-blocking

**Synchronization concept**
- `DatabaseManager` uses the **Singleton pattern** — `getInstance()` ensures only one database connection exists, which is the same principle as preventing race conditions in multithreaded access

---

## I/O Streams

**File I/O**
- SQLite creates a physical file `hotel_management.db` on disk — this is file I/O at the database level
- `DatabaseManager` uses `FileInputStream` equivalent through JDBC — the driver reads/writes the `.db` file as a binary stream

---

## Random Access File, Serialization

**Random Access**
- SQLite's `seek()` equivalent is the SQL `WHERE room_number = ?` query — it jumps directly to a record without reading all rows sequentially, exactly like `RandomAccessFile.seek()`

**Serialization concept**
- Instead of Java object serialization (`ObjectOutputStream`), the project uses database persistence — which is the industry-standard equivalent. `Room` and `Booking` objects are serialized to rows and deserialized back to Java objects through the `mapResultSet()` method in each DAO

---

## Generics

**Generic classes and methods**
- `ObservableList<Room>`, `ObservableList<Booking>` — type-safe collections
- `TableView<Room>`, `TableColumn<Room, String>` — heavily generic JavaFX classes
- The `col()` helper method in `RoomController` is a **generic method**: `private <T> TableColumn<Room, T> col(...)` — it creates columns for any data type T
- `TableCell<Room, Boolean>` for status, `TableCell<Room, Double>` for price — bounded generics

---

## Collections Framework

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

## JavaFX GUI

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
