# Postman Collection: Automated Aircraft Testing

This Postman collection automates the creation and validation of an `aircraft` entity using the Flight Ticket Booking API. It leverages pre-request scripts and test scripts to ensure robust, repeatable API testing.

## Overview

The collection contains a single `GET` request that internally:
1. Executes a `POST` request in the **Pre-request Script** to create a new aircraft with **randomized data**.
2. Stores all response fields (including the generated `id`) as **collection variables**.
3. Fetches the created aircraft using the saved `id` via the `GET` endpoint.
4. Runs a comprehensive suite of **test scripts** against the `GET` response.

This approach guarantees that each run validates a fresh, dynamic entity and verifies both data persistence and API contract adherence.

## How It Works

### 1. Pre-request Script (Executed before the GET call)
- Generates random values for all aircraft properties (e.g., `manufacturer`, `model`, `numberOfSeats`).
- Sends a `POST /api/v0/aircrafts` request with the generated payload.
- Parses the response and saves **every field** as a collection variable (e.g., `aircraftId`, `manufacturer`, `model`, `numberOfSeats`, `flights`).

### 2. GET Request
- Uses the `aircraftId` variable to call `GET /api/v0/aircrafts/{id}`.

### 3. Tests (Executed after the GET call)
The following assertions are performed automatically:

| Test Name | Description |
|-----------|-------------|
| `GET status is 200` | Verifies that the request succeeded. |
| `Response matches AircraftDto schema` | Validates the JSON structure against the expected DTO schema. |
| `numberOfSeats is within [1, 1000]` | Ensures the seat count complies with business rules. |
| `manufacturer length <= 300 (Swagger contract)` | Checks max length constraint from the API specification. |
| `model length <= 300 (Swagger contract)` | Checks max length constraint from the API specification. |
| `GET data matches POST data` | Compares all fields returned by GET with the original values sent in POST (ignoring `id` if necessary). |
| `GET aircraftId matches saved ID` | Confirms that the ID in the response matches the one generated earlier. |
| `flightIds is an array` | Verifies that the `flightIds` field is of type array (even if empty). |

## Requirements

- Postman v10 or newer
- Access to the Flight Ticket Booking API (base URL configured via `{{BaseUrl}}` variable)

## Usage

1. Import this collection into Postman.
2. Set up an environment with the following variable:
   - `BaseUrl` – e.g., `http://192.168.40.100:8245`
3. Ensure the API is reachable and authentication (if required) is configured.
4. Run the `GET Get aircraft by ID (with auto-creation)` request.
5. View test results in the **Test Results** tab.

## Collection Structure
Automated Aircraft Testing
└── GET Get aircraft by ID (with auto-creation)
├── Pre-request Script: creates random aircraft via POST
└── Tests: all validation assertions

text

## Notes

- Random data generation includes realistic strings and numeric ranges to catch boundary issues.
- The collection uses collection variables (not environment) to avoid polluting environment scope and to keep test data isolated to the run.

## Contributing

Feel free to extend this collection by adding more validation checks or by using the same pattern for other entities (e.g., flights, airports).
