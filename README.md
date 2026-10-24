# AggieRide
A rideshare app for students, by students

## Tech Stack

### Client (Mobile)

| Layer | Technology |
|-------|------------|
| Framework | React Native + TypeScript (Expo) |
| Maps SDK | Google Maps SDK for Android/iOS |
| Push Notifications | Firebase Cloud Messaging for Android, Apple Push Notifications for iOS |

### Backend

| Layer | Technology |
|-------|------------|
| API Framework | Node.js + TypeScript with NestJS |
| Protocol | REST |
| Real-time | WebSockets via Socket.IO |

### Authentication

| Purpose | Technology |
|---------|------------|
| Primary | University SSO via OIDC/SAML (Auth0, Okta, or Keycloak) |
| Fallback | .edu email verification + manual ID check |

### Database & Storage

| Purpose | Technology |
|---------|------------|
| Primary Database | PostgreSQL + PostGIS |
| Caching | Redis |
| Object Storage | AWS S3 or GCP Cloud Storage |

### External APIs

| Purpose | API |
|---------|-----|
| Autocomplete | Google Places API |
| Geocoding | Google Geocoding API |
| Routing & Distance | Google Directions API / Distance Matrix API |
| Fuel Prices | EIA API (U.S. Energy Information Administration) |

### Payments

| Purpose | Technology |
|---------|------------|
| Payment Processing | Stripe Payments |
| Payouts | Stripe Connect |
