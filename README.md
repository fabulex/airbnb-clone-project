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

## Features Overview
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


(Planned)
