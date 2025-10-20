# AirBnB Clone Project

## Overview
The backend for the Airbnb Clone project is designed to provide a robust and scalable foundation for managing user interactions, property listings, bookings, and payments. This backend will support various functionalities required to mimic the core features of Airbnb, ensuring a smooth experience for users and hosts.

## Project Goals
- User Management: Implement a secure system for user registration, authentication, and profile management.
- Property Management: Develop features for property listing creation, updates, and retrieval.
- Booking System: Create a booking mechanism for users to reserve properties and manage booking details.
- Payment Processing: Integrate a payment system to handle transactions and record payment details.
- Review System: Allow users to leave reviews and ratings for properties.
- Data Optimization: Ensure efficient data retrieval and storage through database optimizations.

## Feature Breakdown
1. API Documentation
OpenAPI Standard: The backend APIs are documented using the OpenAPI standard to ensure clarity and ease of integration.
Django REST Framework: Provides a comprehensive RESTful API for handling CRUD operations on user and property data.
GraphQL: Offers a flexible and efficient query mechanism for interacting with the backend.
2. User Authentication
Endpoints: /users/, /users/{user_id}/
Features: Register new users, authenticate, and manage user profiles.
3. Property Management
Endpoints: /properties/, /properties/{property_id}/
Features: Create, update, retrieve, and delete property listings.
4. Booking System
Endpoints: /bookings/, /bookings/{booking_id}/
Features: Make, update, and manage bookings, including check-in and check-out details.
5. Payment Processing
Endpoints: /payments/
Features: Handle payment transactions related to bookings.
6. Review System
Endpoints: /reviews/, /reviews/{review_id}/
Features: Post and manage reviews for properties.
7. Database Optimizations
Indexing: Implement indexes for fast retrieval of frequently accessed data.
Caching: Use caching strategies to reduce database load and improve performance.

## Technology Stack
- Django: A high-level Python web framework used for building the RESTful API.
- Django REST Framework: Provides tools for creating and managing RESTful APIs.
- PostgreSQL: A powerful relational database used for data storage.
- GraphQL: Allows for flexible and efficient querying of data.
- Celery: For handling asynchronous tasks such as sending notifications or processing payments.
- Redis: Used for caching and session management.
- Docker: Containerization tool for consistent development and deployment environments.
- CI/CD Pipelines: Automated pipelines for testing and deploying code changes.

## Team Roles
- Backend Developer: Responsible for implementing API endpoints, database schemas, and business logic.
- Database Administrator: Manages database design, indexing, and optimizations.
- DevOps Engineer: Handles deployment, monitoring, and scaling of the backend services.
- QA Engineer: Ensures the backend functionalities are thoroughly tested and meet quality standards.

## Database Design
The database for the Airbnb Clone backend is designed using a relational model in PostgreSQL. It emphasizes normalization to reduce redundancy, efficient querying through indexing, and scalability for high-traffic scenarios. Below, we outline the key entities, their important fields (focusing on 3-5 core attributes per entity), and the relationships between them. These entities capture the core data for user management, property listings, bookings, reviews, and payments.

###Entities and Fields
1. Users - Represents registered users, who can act as guests (booking properties) or hosts (listing properties).

- id: Primary key (UUID or Auto-increment integer) – Unique identifier for the user.
- email: String (unique) – User's email address for login and notifications.
- password_hash: String – Securely hashed password for authentication.
- role: Enum (e.g., 'guest', 'host', 'both') – Defines user type for access control.
- profile_picture_url: String (optional) – URL to user's profile image.

2. Properties - Represents listings created by hosts, including details for potential bookings.

- id: Primary key (UUID or Auto-increment integer) – Unique identifier for the property.
- title: String – Short descriptive name of the property.
- description: Text – Detailed overview of amenities and features.
- price_per_night: Decimal – Base rental cost per night.
- location: String (or JSON for lat/long) – Address or coordinates for searchability.
- host_id: Foreign key to Users – Links to the owning user.

3. BookingsCaptures reservations made by guests for specific properties and dates.id: Primary key (UUID or Auto-increment integer) – Unique identifier for the booking.

- check_in_date: Date – Start date of the stay.
- check_out_date: Date – End date of the stay.
- total_price: Decimal – Calculated total cost including fees.
- status: Enum (e.g., 'pending', 'confirmed', 'cancelled') – Current state of the booking.
- guest_id: Foreign key to Users – Links to the booking user.
- property_id: Foreign key to Properties – Links to the reserved property.

4. Reviews -Stores feedback from guests about their stay, influencing property ratings.

- id: Primary key (UUID or Auto-increment integer) – Unique identifier for the review.
- rating: Integer (1-5) – Star rating for the property.
- comment: Text – Written feedback from the guest.
- created_at: Timestamp – Date and time the review was submitted.
- guest_id: Foreign key to Users – Links to the reviewer.
- property_id: Foreign key to Properties – Links to the reviewed property.
- booking_id: Foreign key to Bookings (optional) – Ties to a specific booking for context.

5. Payments - Handles transaction records for bookings, integrating with external processors.

- id: Primary key (UUID or Auto-increment integer) – Unique identifier for the payment.
- amount: Decimal – Total amount charged.
- status: Enum (e.g., 'pending', 'completed', 'failed') – Payment processing state.
- transaction_id: String – External processor's reference ID.
- created_at: Timestamp – Date and time of the payment attempt.
- booking_id: Foreign key to Bookings – Links to the associated booking.

6. Relationships

- Users to Properties: One-to-Many – A single User (host) can own multiple Properties, but each Property belongs to exactly one User (via host_id).
- Users to Bookings: One-to-Many – A single User (guest) can make multiple Bookings, but each Booking is tied to exactly one User (via guest_id).
- Properties to Bookings: One-to-Many – A single Property can have multiple Bookings over time, but each Booking is for exactly one Property (via property_id).
- Users to Reviews: One-to-Many – A single User (guest) can leave multiple Reviews, but each Review is authored by exactly one User (via guest_id).
- Properties to Reviews: One-to-Many – A single Property can receive multiple Reviews, but each Review pertains to exactly one Property (via property_id).
- Bookings to Reviews: One-to-One (optional) – A Booking can have at most one Review (post-stay), linked via booking_id for validation.
- Bookings to Payments: One-to-One – Each Booking requires exactly one Payment record (via booking_id), ensuring transactional integrity.

This design supports efficient joins for queries like "fetch all bookings for a property with guest details" and includes indexes on foreign keys (e.g., host_id, property_id) and frequent search fields (e.g., location, email) for performance. Future extensions could include many-to-many relationships, such as user wishlists for properties.

## API Security
1. Authentication  
- Implementation: Token-based authentication using JSON Web Tokens (JWT) via djangorestframework-simplejwt. Users must authenticate via /auth/login/ or /auth/register/ endpoints, receiving a short-lived access token and refresh token. All protected endpoints require a valid Authorization: Bearer <token> header. GraphQL queries/mutations enforce the same via middleware.  
- Why Crucial: Prevents unauthorized access to personal data (e.g., user profiles, booking history). In an Airbnb-like system, unauthenticated requests could expose hosts' property details or guests' travel plans, leading to privacy violations or stalking risks.

2. Authorization  
- Implementation: Role-based access control (RBAC) using Django's permission classes in DRF (e.g., IsAuthenticated, custom IsHostOrReadOnly for properties). For example, only the property's host can update/delete it (PUT/DELETE /properties/{id}/), while guests can only read or book. GraphQL uses context-based resolvers to enforce permissions. Foreign key checks in models prevent cross-user data manipulation.  
- Why Crucial: Ensures users can only interact with their own data, protecting against privilege escalation. For instance, a malicious user couldn't alter another host's listings or view competitors' earnings, safeguarding business integrity and preventing disputes.

3. Rate Limiting  
- Implementation: Applied via django-ratelimit on high-risk endpoints (e.g., login attempts limited to 5/hour per IP, booking creation to 10/day per user). Redis is used as the backend for distributed rate limiting in a scaled environment. Global limits (e.g., 100 requests/minute) prevent API abuse.  
- Why Crucial: Mitigates denial-of-service (DoS) attacks and brute-force attempts on authentication. In a booking system, unchecked requests could overwhelm servers during peak times (e.g., holidays), causing downtime and lost revenue.

4. Input Validation and Sanitization  
- Implementation: DRF serializers enforce strict validation (e.g., email format, date ranges for bookings). SQL injection is prevented via Django's ORM query parameterization. XSS/CSRF risks are handled with CSRF tokens in non-API views and output escaping in responses. File uploads (e.g., property images) are scanned and stored securely on cloud storage (e.g., AWS S3 with signed URLs).  
- Why Crucial: Blocks malicious payloads that could compromise the database or frontend. For user data entry (e.g., reviews), invalid inputs could lead to injection attacks, exposing PII like addresses in property locations.

5. Data Encryption and Secure Transmission  
- Implementation: All traffic enforced over HTTPS via Django's SECURE_SSL_REDIRECT. Sensitive fields (e.g., passwords) are hashed with PBKDF2/Argon2; payment details are never stored—integrated with Stripe for tokenization. Database connections use SSL, and Redis sessions are encrypted.  
- Why Crucial: Protects payments from interception (e.g., man-in-the-middle attacks), ensuring PCI compliance. For an app handling financials and travel data, unencrypted transmissions could result in fraud, identity theft, or regulatory fines.

6. Auditing and Monitoring  
- Implementation: Celery tasks log all critical actions (e.g., booking confirmations) to a secure audit trail. Integration with tools like Sentry for error tracking and Prometheus for anomaly detection. Regular security audits via bandit for code scans.  
- Why Crucial: Enables quick incident response and forensic analysis. In reviews or payments, undetected anomalies could allow fraud (e.g., fake bookings), eroding trust and leading to legal liabilities.

Security for Key Project Areas
- User Management: Authentication and authorization protect profiles from unauthorized views/edits, crucial for privacy (e.g., GDPR compliance on emails and photos).  
- Property Management: RBAC prevents tampering with listings, vital for hosts' revenue and accurate search results.  
- Booking System: Rate limiting and validation secure reservations, avoiding overbooking exploits or spam that could disrupt availability.  
- Payment Processing: Encryption and non-storage of card data ensure secure transactions, preventing chargeback fraud and maintaining financial trust.  
- Review System: Sanitization stops spam or abusive content, fostering a reliable feedback ecosystem without defamation risks.



## CI/CD Pipeline (Planned)
