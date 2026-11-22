# AggieRide — Team Task Distribution & Feature Specifications

## Frontend Team (2 members)

| Member | Ownership | Tasks | Languages & Tools |
|---|---|---|---|
| **Sanjana** | Passenger experience | Ride search and filters; ride detail screens; route/map previews; seat booking; booking status and passenger trip history | TypeScript, TSX, React Native, Expo, Expo Router, Google Maps SDK, TanStack Query, Figma |
| **Hadi** | Driver experience & shared mobile foundation | Authentication screens; profile management; ride creation; driver dashboard; reservation management; notification UI; payment/payout onboarding screens; shared UI components and navigation | TypeScript, TSX, React Native, Expo, Expo Router, Zustand, TanStack Query, Figma |

**Shared frontend responsibilities:** Maintain one Figma design system (buttons, inputs, spacing, navigation, loading/empty/error states); review API contracts before implementing screens; integrate generated typed API clients; test complete user flows on devices.

## Backend Team (9 members)

| Member | Ownership | Tasks | Languages & Tools |
|---|---|---|---|
| **Iaro** | **API Integration Lead & Backend Architecture** | NestJS project structure, API conventions and documentation, shared error handling, module boundaries, API contract approvals, integration reviews | TypeScript, NestJS, Node.js, Swagger/OpenAPI, Docker |
| **Alex** | Authentication & User Management | University email verification / approved SSO, login, auth token handling, user/driver profiles, role-based access, account management | TypeScript, SQL, NestJS, Auth0/Clerk or OIDC, PostgreSQL |
| **Felipe** | Database & Geospatial Data | PostgreSQL schema, migrations, relationships, indexes, PostGIS setup, database constraints, query optimization | SQL, TypeScript, PostgreSQL, PostGIS, Drizzle ORM |
| **Aditya** | Ride Management & Reservations | Create/edit/cancel rides, ride listings, seat reservations, booking status and cancellation, transaction-safe seat allocation | TypeScript, SQL, NestJS, Drizzle, PostgreSQL, Jest/Vitest |
| **Josh** | Maps, Routes & Ride Matching | Google Places/geocoding, route/distance calculation, location filtering, ride-match ranking, detour estimation | TypeScript, SQL, NestJS, Google Routes API, Places API, PostGIS, Redis |
| **William** | Payments & Payouts | Stripe payments, Stripe Connect onboarding, payment/refund states, webhook handling, idempotency, transaction records | TypeScript, SQL, NestJS, Stripe SDK/CLI, PostgreSQL |
| **Haasika** | Real-Time Events & Notifications | Socket.IO booking/status events, push notification triggers, scheduled reminders, retryable background jobs | TypeScript, NestJS, Socket.IO, Redis, BullMQ, Expo Notifications |
| **Himesh** | DevOps, Security & Quality Infrastructure | CI/CD, staging deployment, Docker configuration, secret management, monitoring, backups, rate limiting, automated test infrastructure | Bash, YAML, TypeScript, Docker, GitHub Actions, AWS/GCP, Sentry, Jest/Vitest |
| **Hadi** | Backend API Integration & Contract Testing (support) | Maintain generated TypeScript API client and frontend/backend contract compatibility; test cross-module integration and key failure cases; coordinate driver-side API requirements with Iaro | TypeScript, OpenAPI, Orval/openapi-typescript, NestJS, Jest/Vitest, Postman/Bruno |

**Note:** Hadi is listed on **both teams** as requested. Their backend role is intentionally a supporting role so their primary frontend workload remains feasible. All backend members test their own modules; Himesh owns shared quality infrastructure, not all testing.

## Frontend–Backend Feature Ownership

| Feature / Screen | Frontend Owner | Backend Owner(s) | Key API / Behavior |
|---|---|---|---|
| Login & registration | Hadi | Alex; Iaro (contract) | Student verification, login, session/token handling |
| User & driver profiles | Hadi | Alex | View/update profile and driver information |
| Create/edit a ride | Hadi | Aditya; Josh (places/routes) | Create, update, cancel ride listings |
| Ride search & filters | Sanjana | Aditya, Josh | Query rides by location, date, seats, etc. |
| Ride details & map | Sanjana | Aditya, Josh | Retrieve ride details, route preview, available seats |
| Seat booking & cancellation | Sanjana | Aditya; Felipe (data integrity) | Reserve/cancel seats; prevent overbooking |
| Driver reservation dashboard | Hadi | Aditya, Haasika | View reservations and receive booking updates |
| Payments / booking checkout | Sanjana | William; Aditya | Initiate/confirm payment and show transaction state |
| Driver payout onboarding | Hadi | William | Stripe Connect onboarding/status |
| Notifications | Hadi | Haasika | Push notifications, reminders, and ride status updates |
| API client & end-to-end integration | Sanjana, Hadi | Iaro, Hadi; relevant feature owner | OpenAPI contracts, generated client, staging integration |

## Feature Specification — Use This Template for Every Screen

| Field | What to Record |
|---|---|
| **Feature / Screen** | Name and user goal |
| **Frontend owner** | Sanjana or Hadi |
| **Backend owner(s)** | Responsible backend developer(s) |
| **Figma link** | Screen design and applicable UI states |
| **UI elements and actions** | Buttons, inputs, dropdowns, data displays, and what each does |
| **API contract** | HTTP method, endpoint, authentication, request data, response fields |
| **Success behavior** | What the UI shows/updates after success |
| **Failure behavior** | Validation, empty, loading, unauthorized, conflict, and server-error states |
| **Real-time behavior** | Socket.IO event or refresh rule, if applicable |
| **Dependencies** | Required authentication, models, services, or other features |
| **Acceptance criteria** | Conditions needed to call the feature complete |

### Example Feature Specification — Ride Booking Screen

| Field | Specification |
|---|---|
| **Frontend owner** | Sanjana |
| **Backend owner(s)** | Aditya (booking API), Felipe (transaction safety), Haasika (events), Iaro (contract review) |
| **UI elements and actions** | Ride details and available seats displayed from API; seat selector updates local state; **Book Ride** submits reservation; confirmation/error message reflects API result |
| **API contract (proposed)** | `GET /api/v1/rides/:id` and `POST /api/v1/rides/:id/reservations` with `{ "seats": 2 }` |
| **Success response (example)** | `201` with `reservationId`, `status`, `seatsReserved`, `remainingSeats` |
| **Success behavior** | Show confirmation, update booking status and remaining seats |
| **Failure behavior** | `400` invalid seats; `401` prompt login; `409` insufficient seats and refresh; `500` display retry option |
| **Real-time behavior** | Refresh ride availability or handle a defined Socket.IO booking update |
| **Acceptance criteria** | User can select seats; button disables during submission; duplicate taps do not create duplicate bookings; insufficient seats handled; successful booking displayed correctly |

**For every feature:** Frontend and backend owners agree on the contract before implementation. Iaro reviews changes to shared API conventions. Frontend can build with mock responses while backend implements the endpoint; both test the real flow together in staging before marking it complete.
