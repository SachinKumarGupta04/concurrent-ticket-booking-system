# Concurrent Ticket Booking System with Seat Locking and Confirmation

## Objective

Learn how to implement a ticket booking system in Node.js that handles concurrent seat reservation requests safely using a seat locking mechanism. This task helps you understand how to manage in-memory state, handle concurrent access, and design a system that prevents race conditions during booking.

## Task Description

Create a Node.js and Express.js application that simulates a ticket booking system for events or movie theaters. Implement endpoints to view available seats, temporarily lock a seat for a user, and confirm the booking. Design a seat locking mechanism so that when a seat is locked, it cannot be locked or booked by other users until it is either confirmed or the lock expires automatically (for example, after 1 minute). Store seat states in an in-memory data structure for simplicity. Include clear success and error messages for different scenarios, such as trying to lock an already locked or booked seat, or confirming a seat without a lock. Test your API by simulating concurrent requests to demonstrate that the locking logic correctly prevents double booking and ensures reliable seat allocation.

## Features

- View available seats
- Temporarily lock seats for users
- Confirm seat bookings
- Automatic lock expiration (1 minute timeout)
- Prevent race conditions and double booking
- Clear success and error messages
- In-memory state management

## Prerequisites

Before you begin, ensure you have the following installed:

- **Node.js** (v14 or higher) - [Download Node.js](https://nodejs.org/)
- **npm** (comes with Node.js) or **yarn**
- A code editor (VS Code, Sublime Text, etc.)
- An API testing tool (Postman, Thunder Client, or curl)

## Setup Instructions

### 1. Clone the Repository

```bash
git clone https://github.com/SachinKumarGupta04/concurrent-ticket-booking-system.git
cd concurrent-ticket-booking-system
```

### 2. Initialize Node.js Project

If starting from scratch:

```bash
npm init -y
```

### 3. Install Dependencies

Install Express.js and other required packages:

```bash
npm install express
npm install --save-dev nodemon
```

### 4. Project Structure

Create the following file structure:

```
concurrent-ticket-booking-system/
├── node_modules/
├── src/
│   ├── server.js
│   ├── routes/
│   │   └── bookingRoutes.js
│   ├── controllers/
│   │   └── bookingController.js
│   └── models/
│       └── seatManager.js
├── package.json
├── package-lock.json
└── README.md
```

### 5. Configure package.json Scripts

Add the following scripts to your `package.json`:

```json
"scripts": {
  "start": "node src/server.js",
  "dev": "nodemon src/server.js",
  "test": "node test/concurrentTest.js"
}
```

### 6. Run the Application

For development (with auto-reload):

```bash
npm run dev
```

For production:

```bash
npm start
```

The server will start on `http://localhost:3000` (or the port you configure).

## API Endpoints

### 1. View Available Seats

```
GET /api/seats
```

Returns the current state of all seats.

### 2. Lock a Seat

```
POST /api/seats/lock
Content-Type: application/json

{
  "seatId": "A1",
  "userId": "user123"
}
```

Temporarily locks a seat for 1 minute.

### 3. Confirm Booking

```
POST /api/seats/confirm
Content-Type: application/json

{
  "seatId": "A1",
  "userId": "user123"
}
```

Confirms the booking for a previously locked seat.

### 4. Release Lock (Optional)

```
POST /api/seats/release
Content-Type: application/json

{
  "seatId": "A1",
  "userId": "user123"
}
```

Manually releases a lock before expiration.

## Testing Concurrent Requests

To test the concurrent booking functionality:

1. Use tools like **Apache JMeter**, **Artillery**, or write custom scripts
2. Simulate multiple users trying to book the same seat simultaneously
3. Verify that only one user can successfully lock and book each seat
4. Test lock expiration by locking a seat and waiting for 1 minute

### Example Test Script (Node.js)

```javascript
const axios = require('axios');

async function testConcurrentBooking() {
  const promises = [];
  
  // Simulate 10 users trying to book the same seat
  for (let i = 0; i < 10; i++) {
    promises.push(
      axios.post('http://localhost:3000/api/seats/lock', {
        seatId: 'A1',
        userId: `user${i}`
      })
    );
  }
  
  const results = await Promise.allSettled(promises);
  console.log(results);
}

testConcurrentBooking();
```

## Implementation Guidelines

### Seat State Management

Each seat should have the following states:
- **available**: Seat is free to book
- **locked**: Seat is temporarily reserved
- **booked**: Seat is confirmed and unavailable

### Data Structure Example

```javascript
const seats = {
  'A1': { status: 'available', userId: null, lockedAt: null },
  'A2': { status: 'available', userId: null, lockedAt: null },
  // ... more seats
};
```

### Lock Expiration Logic

```javascript
function checkLockExpiration(seat) {
  if (seat.status === 'locked') {
    const currentTime = Date.now();
    const lockDuration = currentTime - seat.lockedAt;
    
    if (lockDuration > 60000) { // 60 seconds
      seat.status = 'available';
      seat.userId = null;
      seat.lockedAt = null;
    }
  }
}
```

## Error Scenarios to Handle

1. Attempting to lock an already locked seat
2. Attempting to lock a booked seat
3. Attempting to confirm without a valid lock
4. Lock expiration before confirmation
5. Invalid seat IDs
6. Missing required parameters

## Technologies Used

- **Node.js**: JavaScript runtime
- **Express.js**: Web framework
- **JavaScript**: Programming language
- In-memory data structures (no database required)

## Learning Outcomes

By completing this project, you will learn:

- How to handle concurrent requests in Node.js
- Race condition prevention techniques
- Time-based state management
- RESTful API design
- Error handling and validation
- In-memory state management
- Testing concurrent operations

## Contributing

Feel free to fork this repository and submit pull requests for improvements or additional features.

## License

This project is open source and available for educational purposes.

## Support

If you encounter any issues or have questions, please open an issue in this repository.
