# Task Distribution & Feature Specifications

## Frontend

| Member | Ownership | Tasks | Languages & Tools |
|---|---|---|---|
| **Sanjana** | Passenger UI/UX + shared features | Authentication screens, ride search and filters, ride detail screens, route/map previews, seat booking, booking status, passenger trip history, notification UI, payment/payout onboarding screens, shared UI and navigation components | TypeScript, TSX, React Native, Expo, Expo Router, Google Maps SDK, Figma |
| **Hadi** | Driver UI/UX | ride creation, driver dashboard, reservation management | TypeScript, TSX, React Native, Expo, Expo Router, Figma |


## Backend Team

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


## Frontend–Backend Feature Ownership

| App screen / Feature | Frontend owner | Backend owner(s) | Key Behavior |
|---|---|---|---|
| Login / Registration | Sanjana | Alex | Student verification, login, session/token handling |
| Home / Dashboard | Sanjana | Aditya | User landing screen with links to relevant |
| Profile | Hadi | Josh | View/update profile and user information |
| Post/Edit a ride | Hadi | Hadi | Create, update, cancel ride listings |
| Search rides | Sanjana | William | Query rides by location, date, seats, etc. |
| Ride details & map | Sanjana | Haasika | Retrieve ride details, route preview, available seats |
| Seat booking & cancellation | Sanjana | Felipe | Reserve/cancel seats; prevent overbooking |
| My Trips (rider) | Sanjana | Felipe | View upcoming and past rides |
| Driver dashboard | Hadi | Himesh | View reservations and receive booking updates |
| Payment checkout | Sanjana | Aditya | Initiate/confirm payment and show transaction state |
| Notifications | Sanjana | Josh | Push notifications, reminders, and ride status updates |
| API client & end-to-end integration, database schema | Sarthak | Sarthak, Iaro | OpenAPI contracts, generated client, database setup |

## Feature Specifications

## 1. Login / Registration

| Field | Specification |
|---|---|
| Frontend owner | Sanjana |
| Backend owner | Alex |
| Core behavior | Student verification, login, session/token handling |

| UI element / Action | Expected behavior | Proposed API / Integration | States / Validation |
|---|---|---|---|
| University email field | Collect student's university email | `POST /auth/register` | Required; validate approved email domain |
| Sign up / Continue | Start registration and student verification | `POST /auth/register` | Loading; existing account; invalid email |
| Verification code / link | Confirm control of email address | `POST /auth/verify-email` | Pending; expired/invalid code; verified |
| Sign in button | Authenticate through selected auth provider | Auth provider / `POST /auth/login` (if applicable) | Loading; invalid credentials; signed in |
| Session initialization | Load current user and verification state | `GET /users/me` | Unauthorized; incomplete verification; active |
| Sign out | Clear local session and revoke/end provider session where supported | Auth provider logout | Logged-out screen; expired session |

## 2. Home / Dashboard

| Field | Specification |
|---|---|
| Frontend owner | Sanjana |
| Backend owner | Aditya |
| Core behavior | User landing screen with links to relevant ride features |

| UI element / Action | Expected behavior | Proposed API / Integration | States / Validation |
|---|---|---|---|
| Welcome header | Show signed-in student's name | `GET /users/me` | Loading; name fallback |
| Find a Ride | Open ride search screen | Frontend navigation | N/A |
| Post a Ride | Open ride posting screen | Frontend navigation | N/A |
| Upcoming trips preview | Show user's next passenger reservations | `GET /users/me/reservations?status=upcoming` | Empty; loading; error |
| My posted rides preview | Show user's next offered rides | `GET /users/me/rides?status=upcoming` | Empty; loading; error |
| Profile / Notifications shortcuts | Navigate to the relevant screens | Frontend navigation | Unread badge optional |

## 3. Profile

| Field | Specification |
|---|---|
| Frontend owner | Hadi |
| Backend owner | Josh |
| Core behavior | View/update profile and user information |

| UI element / Action | Expected behavior | Proposed API / Integration | States / Validation |
|---|---|---|---|
| Profile summary | Show name, university email, verification status | `GET /users/me` | Loading; account not found |
| Editable fields | Edit permitted profile fields (e.g., display name, phone) | Local form state | Field validation; unchanged email |
| Save changes | Persist permitted profile updates | `PATCH /users/me` | Saving; validation error; success |
| Verification indicator | Show student verification state | `GET /users/me` | Verified; pending; unverified |
| Account / Logout | End session | Auth provider logout | Return to login |

## 4. Post / Edit a Ride

| Field | Specification |
|---|---|
| Frontend owner | Hadi |
| Backend owner | Hadi |
| Core behavior | Create, update, and cancel ride listings |

| UI element / Action | Expected behavior | Proposed API / Integration | States / Validation |
|---|---|---|---|
| Pickup and destination | Select route endpoints | Places autocomplete or location input | Required; valid locations |
| Departure date/time | Choose future departure time | Local date/time picker | Required; future date |
| Available seats | Set passenger capacity | Local numeric input | Positive integer; capacity limit |
| Price per seat | Enter proposed ride price | Local currency input | Nonnegative amount; send cents |
| Post Ride | Create listing linked to authenticated driver | `POST /rides` | Loading; validation error; created |
| Load existing ride | Prepopulate edit form | `GET /rides/:rideId` | Missing ride; not owner |
| Save edits | Change permitted fields of owned ride | `PATCH /rides/:rideId` | Conflict with booked seats; success |
| Cancel ride | Cancel an owned listing after confirmation | `POST /rides/:rideId/cancel` | Already cancelled; permission error |

## 5. Search Rides

| Field | Specification |
|---|---|
| Frontend owner | Sanjana |
| Backend owner | William |
| Core behavior | Query rides by location, date, seats, and other filters |

| UI element / Action | Expected behavior | Proposed API / Integration | States / Validation |
|---|---|---|---|
| Origin input | Enter/select preferred pickup area | Places autocomplete or location input | Optional/required per search policy |
| Destination input | Enter/select preferred drop-off area | Places autocomplete or location input | Optional/required per search policy |
| Date selector | Filter by departure date | Local date picker | Valid date |
| Seats selector | Filter rides with enough available seats | Local selector | Positive integer |
| Search / Apply filters | Fetch matching available rides | `GET /rides?origin=...&destination=...&date=...&seats=...` | Loading; invalid query; server error |
| Results list | Show driver, departure, route, price, and seats | `GET /rides` response | No matches; pagination/refresh |
| Ride card / View details | Open selected ride | Frontend navigation to ride details | N/A |

## 6. Ride Details & Map

| Field | Specification |
|---|---|
| Frontend owner | Sanjana |
| Backend owner | Haasika |
| Core behavior | Retrieve ride details, route preview, and available seats |

| UI element / Action | Expected behavior | Proposed API / Integration | States / Validation |
|---|---|---|---|
| Ride heading | Show origin, destination, and departure | `GET /rides/:rideId` | Loading; not found; cancelled |
| Driver section | Show public driver information | `GET /rides/:rideId` | Hide private fields |
| Seat availability | Display current remaining seats | `GET /rides/:rideId` | Full; low availability |
| Price section | Show price per seat and currency | `GET /rides/:rideId` | Use backend amount, not hard-coded price |
| Route preview / map | Plot pickup and destination and show route when available | `GET /rides/:rideId/route` or included route data; Maps SDK | Map loading; missing route; fallback locations |
| Book Ride | Open booking flow for selected ride | Frontend navigation | Disabled if unavailable/cancelled |

## 7. Seat Booking & Cancellation

| Field | Specification |
|---|---|
| Frontend owner | Sanjana |
| Backend owner | Felipe |
| Core behavior | Reserve/cancel seats; prevent overbooking |

| UI element / Action | Expected behavior | Proposed API / Integration | States / Validation |
|---|---|---|---|
| Ride / seat summary | Display selected ride, price, and availability | `GET /rides/:rideId` | Stale availability; cancelled ride |
| Seat quantity | Choose seats to reserve | Local selector | Between 1 and current availability |
| Total price | Show expected total from backend price | Derived display; server recalculates on submission | Currency formatting |
| Reserve Seat(s) | Atomically create reservation and hold/remove available seats | `POST /rides/:rideId/reservations` | Submitting; full (`409`); authenticated; success |
| Reservation confirmation | Display reservation ID and status | Create reservation response | `pending_payment` or `confirmed` per flow |
| View reservation | Retrieve current booking/status | `GET /reservations/:reservationId` | Not owner; cancelled; expired |
| Cancel booking | Cancel user's reservation and release seats per policy | `POST /reservations/:reservationId/cancel` | Confirm action; cancellation cutoff; success |

## 8. My Trips (Rider)

| Field | Specification |
|---|---|
| Frontend owner | Sanjana |
| Backend owner | Felipe |
| Core behavior | View upcoming and past rides |

| UI element / Action | Expected behavior | Proposed API / Integration | States / Validation |
|---|---|---|---|
| Upcoming tab | Display future reservations | `GET /users/me/reservations?status=upcoming` | Empty; loading; error |
| Past tab | Display completed/cancelled reservations | `GET /users/me/reservations?status=past` | Empty; loading; error |
| Trip cards | Show route, departure, reserved seats, and booking state | Reservations list response | Pending payment; confirmed; cancelled |
| View ride | Open details for selected trip | `GET /rides/:rideId` | Ride unavailable |
| Cancel reservation | Start cancellation flow | `POST /reservations/:reservationId/cancel` | Confirm action; policy restrictions |
| Refresh | Fetch latest trip status | Reservations list API | Refreshing; network failure |

## 9. Driver Dashboard

| Field | Specification |
|---|---|
| Frontend owner | Hadi |
| Backend owner | Himesh |
| Core behavior | View reservations and receive booking updates |

| UI element / Action | Expected behavior | Proposed API / Integration | States / Validation |
|---|---|---|---|
| My rides | Show driver's published and upcoming rides | `GET /users/me/rides` | Empty; loading; error |
| Reservation count | Show booked and available seats per ride | Driver rides response | Correct totals; no negative seats |
| View passengers / reservations | List permitted passenger booking details | `GET /rides/:rideId/reservations` | Driver-only; empty list |
| Booking update indicator | Reflect new/cancelled reservations | Socket.IO `reservation.created` / `reservation.cancelled`, or polling | Reconnect; duplicate events; refresh fallback |
| Manage ride | Open edit/cancel ride screen | Frontend navigation | Only owned rides |
| Refresh dashboard | Fetch authoritative ride and booking state | `GET /users/me/rides` | Loading; network failure |

## 10. Payment Checkout

| Field | Specification |
|---|---|
| Frontend owner | Sanjana |
| Backend owner | Aditya |
| Core behavior | Initiate/confirm payment and show transaction state |

| UI element / Action | Expected behavior | Proposed API / Integration | States / Validation |
|---|---|---|---|
| Order summary | Show ride, seats, total, and payment state | `GET /reservations/:reservationId` | Missing/expired reservation |
| Pay button | Create or retrieve server-owned Stripe payment session/intent | `POST /payments/intents` | Loading; duplicate tap; provider error |
| PaymentSheet | Collect and confirm payment through Stripe | Stripe React Native SDK | Cancelled; processing; failed |
| Payment confirmation | Fetch server-verified payment/reservation status | `GET /payments/:paymentId` or `GET /reservations/:reservationId` | Processing; succeeded; failed |
| Retry payment | Retry an eligible failed/incomplete payment | Payment API (idempotent handling) | Prevent duplicate charges |
| Payment outcome | Display confirmed booking or actionable failure | Backend status updated from Stripe webhook | Do not trust client callback alone |

## 11. Notifications

| Field | Specification |
|---|---|
| Frontend owner | Sanjana |
| Backend owner | Josh |
| Core behavior | Push notifications, reminders, and ride status updates |

| UI element / Action | Expected behavior | Proposed API / Integration | States / Validation |
|---|---|---|---|
| Notification permission | Ask user to allow push notifications | Expo Notifications / OS permission | Allowed; denied; not requested |
| Device token registration | Associate push token with signed-in user | `POST /users/me/push-tokens` | Missing/invalid token; retry |
| Notification list | Show ride and booking alerts | `GET /users/me/notifications` | Loading; empty; error |
| Notification item | Open related ride or booking | Frontend navigation using notification payload | Invalid/deleted target |
| Mark as read | Update read state | `PATCH /notifications/:notificationId` | Read; not found |
| Booking / ride status alert | Inform affected user about booking, cancellation, and ride changes | Backend event -> Expo Push Service (or FCM/APNs) | Failed delivery; retries; no token |
| Departure reminder | Notify before departure based on scheduled time | Scheduled backend job | Cancelled rides excluded; avoid duplicates |


**For every feature:** Frontend and backend owners must agree on the contract before implementation. Iaro will review changes to shared API conventions. Frontend can build with mock responses while backend implements the endpoint; both test the real flow together in staging before marking it complete.
