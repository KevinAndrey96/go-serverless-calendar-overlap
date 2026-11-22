# ⚡ Go Serverless Calendar Overlap & Double-Booking Detector
> **High-Performance Event Scheduling Conflict Resolver built with Go, Concurrency (Goroutines & Mutex), Clean Architecture, Google Wire DI & AWS Lambda**

<div align="center">

![Go](https://img.shields.io/badge/Go-1.20+-00ADD8?style=for-the-badge&logo=go&logoColor=white)
![AWS Lambda](https://img.shields.io/badge/AWS%20Lambda-Serverless-FF9900?style=for-the-badge&logo=aws-lambda&logoColor=white)
![Serverless Framework](https://img.shields.io/badge/Serverless%20Framework-v3-FD5750?style=for-the-badge&logo=serverless&logoColor=white)
![Clean Architecture](https://img.shields.io/badge/Architecture-Clean%20%2F%20Hexagonal-555555?style=for-the-badge)
![Google Wire](https://img.shields.io/badge/Dependency%20Injection-Google%20Wire-blue?style=for-the-badge)

</div>

---

## 📌 Context & Overview

In distributed calendar systems, hotel reservations, meeting schedulers, and docketing platforms (such as Google Calendar, Calendly, or enterprise legal scheduling), detecting conflicting appointments (**"Double Bookings"**) across arbitrary international timezones is a foundational challenge.

> [!NOTE]
> **Engineering Background:**  
> This project was originally designed as the **technical architecture evaluation for Litera** (a leading US-based legal technology and enterprise workflow company). The problem requires receiving an arbitrary sequence of events with different start, end, and timezone specifications, normalizing them to UTC, and returning the exact sequence of overlapping pairs with high performance and zero false positives.

---

## 🚀 Key Architectural Highlights

1. **⚡ Concurrent Interval Overlap Evaluation:**
   - Instead of a slow $O(N^2)$ sequential scan, the service leverages **Go Goroutines** with `sync.WaitGroup` and `sync.Mutex` synchronization to evaluate event collision matrices in parallel.
   - Built-in pair deduplication: automatically eliminates redundant reciprocal pairs (e.g., ensuring `[1, 2]` is returned and `[2, 1]` is skipped) and discards self-referential comparisons (`event.ID == eventToCheck.ID`).

2. **🌐 Multi-Timezone Normalization to UTC:**
   - Dedicated use case (`ParseEventsToUTCUC`) validates incoming payloads and normalizes all localized timestamps into standardized UTC ISO-8601 timestamps before interval computation, ensuring timezone-agnostic comparison accuracy.

3. **🏛️ Clean Architecture & Use-Case Layering:**
   - `internal/models`: Domain models and custom structured errors (`EventError`).
   - `internal/uc`: Isolated business logic (`ParseEventsToUTCUC`, `FindDoubleBookedEventsUC`).
   - `internal/cx_handler.go`: AWS Lambda entrypoint handler.
   - `internal/di`: Compile-time Dependency Injection container powered by Google's `wire` tool.

4. **☁️ Serverless Cloud Deployment:**
   - Automated packaging and deployment to **AWS Lambda** via **Serverless Framework** (`serverless.yml`).
   - Single-command build and deploy automation via `Makefile` (`make dev`).

5. **🧪 Test-Driven Engineering:**
   - Comprehensive unit testing suite using `testify`, table-driven tests, and fixtures for edge cases (zero overlaps, full overlap, adjacent timestamps, inverted intervals).

---

## 📋 API Specification

### Endpoint: `POST /double-booked`

#### Request Payload:
```json
[
  {
    "id": 1,
    "start": "2023-03-01T09:00:00",
    "end": "2023-03-01T10:30:00",
    "timezone": "America/Bogota"
  },
  {
    "id": 2,
    "start": "2023-03-01T14:00:00",
    "end": "2023-03-01T15:30:00",
    "timezone": "UTC"
  },
  {
    "id": 3,
    "start": "2023-03-01T18:00:00",
    "end": "2023-03-01T19:00:00",
    "timezone": "Asia/Tokyo"
  }
]
```

#### Response (Overlapping Event ID Pairs):
```json
[
  [1, 2]
]
```

---

## ⚡ Quickstart

### Local Testing & Execution

```bash
# Run unit tests
go test ./... -v

# Run with coverage
go test ./... -cover
```

### AWS Lambda Deployment

```bash
# Deploy to AWS (requires AWS credentials configured)
make dev
```

---

## 👤 Author

**Kevin Andrey Herrera Silva**  
*Senior Backend Engineer & Software Architect*  
- LinkedIn: [linkedin.com/in/kevinandrey96](https://www.linkedin.com/in/kevinandrey96/)
- Location: Bogotá, Colombia
