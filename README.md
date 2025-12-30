# Zefy — Home Services Platform (Production System)

## Overview

Zefy is a home services startup where I built and owned the **entire technical stack** end-to-end.

The platform supports:
- User bookings
- Partner assignment
- Real-time tracking
- Admin operations
- Payments and scheduling

The system was built under real production constraints with live users.

---

## My Role

I was responsible for:

- Backend architecture and implementation
- Event-driven microservices
- Two Flutter mobile apps (User + Partner)
- Admin dashboard (React)
- Deployment and infrastructure
- Production debugging and incident fixes

This was not a demo project — it was a live system used by real users.

---

## System Architecture

High-level architecture:

- API Gateway (Fastify)
- Microservices (Node.js / TypeScript)
- RabbitMQ for async workflows
- Redis for sessions and real-time state
- MongoDB for persistence
- WebSockets for live updates
- EC2 deployment with PM2
- Cloudflare Pages for admin frontend

---

## Core Booking Flow (Production)

Zefy’s booking system was designed to guarantee **fast partner assignment, real-time visibility, and reliable execution** under live production conditions.

The flow below describes how a booking moves through the system end-to-end.

---

### 1. Address & Hub Resolution

- Users can manage multiple saved addresses (similar to Swiggy)
- Each address is mapped to a **hub**
- All availability, partner assignment, and SLAs are hub-scoped

This allows efficient partner pooling and predictable response times.

---

### 2. Availability Check & Booking Creation

Before allowing a booking:

- The User App checks if partners are available **right now** for:
  - Selected service
  - Selected hub
  - Selected time window
- This guarantees **partner assignment within 5 minutes**

If available:
- Payment is completed via Razorpay
- Booking is created
- Real-time notifications are sent to the user via Firebase
- A domain event is emitted, making the booking visible in the Admin Console

---

### 3. Partner Assignment (Race-to-Accept)

- Admin Console sends a job request to all available partners in the hub
- Partners receive the request in the Partner App
- **First partner to accept** is assigned to the booking
- User is immediately notified that a partner has been assigned

This model ensures minimal wait time and fair job distribution.

---

### 4. Live Booking Session (WebSockets)

Once a booking is active:
- User App establishes a WebSocket connection
- The connection stays open until booking completion
- All state changes are pushed in real time (no polling)

---

### 5. Partner Enroute Tracking & ETA

- Partner taps **“On My Way”** in the Partner App
- Live GPS coordinates are emitted every ~10 seconds
- User sees:
  - Real-time partner location
  - Live ETA calculated using Google Maps API

This provides Uber-style tracking for home services.

---

### 6. Automated ETA Monitoring

Background services:
- Recalculate partner ETA every 30 seconds
- Detect delays or early arrivals
- Automatically notify the user if:
  - Partner is delayed
  - Partner will arrive earlier than expected

This reduces user anxiety and support calls.

---

### 7. Service Start (OTP-Verified)

- Partner arrives and enters a **start OTP**
- Service start is:
  - Verified
  - Persisted
  - Broadcast via domain events and WebSockets
- Service timer starts immediately
- Timer state is recoverable even if apps restart

This prevents disputes about service start times.

---

### 8. Service Completion & Final Payment

- Partner ends the service using an **end OTP**
- Service duration is calculated
- Any extra charges are computed automatically
- User is prompted to:
  - Pay the extra amount (Razorpay)
  - Submit feedback

---

### 9. Partner History & Ratings

- Partners can view:
  - Completed bookings
  - User ratings
- Ratings directly influence partner reputation and future assignments

---

## Mobile Applications

### User App
- Browse services
- Create bookings
- Track partner in real-time
- View booking history

### Partner App
- Go online/offline
- Accept or reject jobs
- Live location updates
- Job lifecycle management

---

## Admin Dashboard

- View all bookings
- Assign partners manually
- Monitor system state
- Resolve user issues

Built using React + Vite and deployed via Cloudflare Pages.

---

## Production Learnings

Key things I learned by running this system in production:

- Partial failures are normal
- Payments and bookings must never be loosely coupled
- Observability is critical
- Manual recovery paths are required
- Idempotency matters everywhere

These learnings directly inspired the **Event-Driven Booking System** project.

---

## Tech Stack

- TypeScript / Node.js
- Fastify
- RabbitMQ
- Redis
- MongoDB
- Flutter
- React
- AWS EC2
- PM2
- Cloudflare Pages

---

## Why This Matters

This project demonstrates:
- Real production ownership
- Full-stack system thinking
- Experience with failure scenarios
- Ability to ship and maintain systems, not just design them


## Mobile Applications (Production)

### User App — Service Discovery & Booking

![User App – Service Selection](./mobile-app-1.png)

---

### User App — Partner Search & Assignment

![User App – Searching Partner](./mobile-app-2.png)

---

### User App — Live Tracking & ETA

![User App – Partner Enroute](./mobile-app-3.png)

---

### Partner App — Availability & Job Management

![Partner App – Online & Jobs](./mobile-app-4.png)
