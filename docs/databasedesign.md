# Database Design: Restaurant Discovery & Ordering App

This document outlines the relational database architecture powering the Restaurant Discovery & Ordering App. The schema is structured to efficiently manage user profiles, restaurant data, nutritional information, transaction histories, and delivery operations.

---

## Core Entities Overview

| Entity | Purpose |
| :--- | :--- |
| **User** | Stores account credentials, contact details, and geolocation data. |
| **Restaurant** | Contains business information, ratings, pricing tiers, and location coordinates. |
| **Menu_Category** | Organizes menu items into logical sections (e.g., Starters, Mains, Desserts). |
| **Menu_Item** | Holds food/beverage details including calorie counts and pricing. |
| **Dietary_Tag / Item_Tags** | Enables many-to-many dietary filtering (e.g., Vegan, Gluten-Free). |
| **Order** | Master transaction record with totals for price and calories. |
| **Order_Item** | Line-item junction table capturing specific items, quantities, and historical prices. |
| **Payment** | Tracks financial transactions and gateway statuses. |
| **Delivery** | Manages driver assignments, live tracking, and delivery timelines. |
| **Review** | Stores user ratings and feedback to compute average restaurant scores. |

---

## Database Schema

### 1. Users & Authentication
Stores all user-related personal and login data.

| Column | Type | Constraint | Description |
| :--- | :--- | :--- | :--- |
| `user_id` | INT | PRIMARY KEY | Unique account identifier. |
| `name` | VARCHAR | NOT NULL | Display name. |
| `email` | VARCHAR | UNIQUE | Login credential and receipt contact. |
| `password_hash` | VARCHAR | NOT NULL | Securely encrypted password. |
| `phone_number` | VARCHAR | | Contact for delivery coordination. |
| `location_lat` | DECIMAL(10,8) | | Last known latitude. |
| `location_lng` | DECIMAL(11,8) | | Last known longitude. |
| `created_at` | TIMESTAMP | DEFAULT NOW() | Account registration timestamp. |

---

### 2. Restaurants
Contains all venue-level details.

| Column | Type | Constraint | Description |
| :--- | :--- | :--- | :--- |
| `restaurant_id` | INT | PRIMARY KEY | Unique venue identifier. |
| `name` | VARCHAR | NOT NULL | Official restaurant name. |
| `address` | TEXT | NOT NULL | Full physical address. |
| `rating` | DECIMAL(3,2) | DEFAULT 0.00 | Aggregated customer rating. |
| `price_tier` | VARCHAR(4) | | Budget indicator ($, $$, $$$). |
| `delivery_fee` | DECIMAL(6,2) | DEFAULT 0.00 | Standard delivery charge. |
| `latitude` | DECIMAL(10,8) | NOT NULL | Geolocation coordinate. |
| `longitude` | DECIMAL(11,8) | NOT NULL | Geolocation coordinate. |
| `is_open` | BOOLEAN | DEFAULT TRUE | Operational status for order acceptance. |

---

### 3. Menu Categories
Logical grouping of menu items for each restaurant.

| Column | Type | Constraint | Description |
| :--- | :--- | :--- | :--- |
| `category_id` | INT | PRIMARY KEY | Unique category identifier. |
| `restaurant_id` | INT | FOREIGN KEY | References `Restaurant.restaurant_id`. |
| `name` | VARCHAR | NOT NULL | Category name (e.g., "Burgers", "Sides"). |
| `display_order` | INT | | Sorting priority within the menu. |

---

### 4. Menu Items
Individual food or beverage entries.

| Column | Type | Constraint | Description |
| :--- | :--- | :--- | :--- |
| `item_id` | INT | PRIMARY KEY | Unique item identifier. |
| `category_id` | INT | FOREIGN KEY | References `Menu_Category.category_id`. |
| `restaurant_id` | INT | FOREIGN KEY | References `Restaurant.restaurant_id`. |
| `name` | VARCHAR | NOT NULL | Item name (e.g., "Cheeseburger"). |
| `description` | TEXT | | Ingredient or preparation notes. |
| `price` | DECIMAL(8,2) | NOT NULL | Current price in local currency. |
| `calories` | INT | NOT NULL | Total caloric value. |
| `is_available` | BOOLEAN | DEFAULT TRUE | Availability toggle for real-time stock. |

---

### 5. Dietary Tags (Many-to-Many)

**Dietary_Tag** (lookup table)

| Column | Type | Constraint | Description |
| :--- | :--- | :--- | :--- |
| `tag_id` | INT | PRIMARY KEY | Unique tag identifier. |
| `name` | VARCHAR | UNIQUE | Tag label (e.g., "Vegan", "Gluten-Free"). |

**Item_Tags** (junction table)

| Column | Type | Constraint | Description |
| :--- | :--- | :--- | :--- |
| `item_id` | INT | FOREIGN KEY | References `Menu_Item.item_id`. |
| `tag_id` | INT | FOREIGN KEY | References `Dietary_Tag.tag_id`. |

---

### 6. Orders
Master record for each transaction.

| Column | Type | Constraint | Description |
| :--- | :--- | :--- | :--- |
| `order_id` | INT | PRIMARY KEY | Unique order identifier. |
| `user_id` | INT | FOREIGN KEY | References `User.user_id`. |
| `restaurant_id` | INT | FOREIGN KEY | References `Restaurant.restaurant_id`. |
| `total_price` | DECIMAL(10,2) | NOT NULL | Aggregated order cost. |
| `total_calories` | INT | NOT NULL | Sum of all item calories. |
| `delivery_address` | TEXT | NOT NULL | Destination address for fulfillment. |
| `instructions` | TEXT | | Special requests (e.g., gate code). |
| `status` | VARCHAR(20) | DEFAULT 'Pending' | Order lifecycle state (Pending, Preparing, Delivered). |
| `created_at` | TIMESTAMP | DEFAULT NOW() | Submission timestamp. |

---

### 7. Order Items (Line Items)
Captures each product within an order.

| Column | Type | Constraint | Description |
| :--- | :--- | :--- | :--- |
| `order_item_id` | INT | PRIMARY KEY | Unique line-item identifier. |
| `order_id` | INT | FOREIGN KEY | References `Order.order_id`. |
| `item_id` | INT | FOREIGN KEY | References `Menu_Item.item_id`. |
| `quantity` | INT | NOT NULL | Number of units ordered. |
| `unit_price` | DECIMAL(8,2) | NOT NULL | Price at checkout (historical snapshot). |

---

### 8. Payments
Financial transaction records.

| Column | Type | Constraint | Description |
| :--- | :--- | :--- | :--- |
| `payment_id` | INT | PRIMARY KEY | Unique payment identifier. |
| `order_id` | INT | FOREIGN KEY | References `Order.order_id`. |
| `amount` | DECIMAL(10,2) | NOT NULL | Charged total. |
| `method` | VARCHAR(30) | NOT NULL | Payment type (e.g., Credit Card, PayPal). |
| `status` | VARCHAR(20) | DEFAULT 'Pending' | Transaction state (Pending, Successful, Failed). |
| `processed_at` | TIMESTAMP | | Bank clearance timestamp. |

---

### 9. Deliveries
Logistics and tracking information.

| Column | Type | Constraint | Description |
| :--- | :--- | :--- | :--- |
| `delivery_id` | INT | PRIMARY KEY | Unique delivery identifier. |
| `order_id` | INT | FOREIGN KEY | References `Order.order_id`. |
| `driver_name` | VARCHAR | NOT NULL | Assigned delivery personnel. |
| `current_lat` | DECIMAL(10,8) | | Real-time tracking latitude. |
| `current_lng` | DECIMAL(11,8) | | Real-time tracking longitude. |
| `estimated_time` | TIMESTAMP | | Projected arrival time. |
| `delivered_at` | TIMESTAMP | | Actual completion timestamp. |

---

### 10. Reviews
User-generated ratings and comments.

| Column | Type | Constraint | Description |
| :--- | :--- | :--- | :--- |
| `review_id` | INT | PRIMARY KEY | Unique review identifier. |
| `user_id` | INT | FOREIGN KEY | References `User.user_id`. |
| `restaurant_id` | INT | FOREIGN KEY | References `Restaurant.restaurant_id`. |
| `rating` | INT | NOT NULL | Score (1–5 stars). |
| `comment` | TEXT | | Optional written feedback. |
| `created_at` | TIMESTAMP | DEFAULT NOW() | Publication timestamp. |

---

## Entity Relationship Summary

| Relationship | Type | Description |
| :--- | :--- | :--- |
| User → Order | One-to-Many | A user can place many orders. |
| User → Review | One-to-Many | A user can write multiple reviews. |
| Restaurant → Menu_Item | One-to-Many | A restaurant offers many menu items. |
| Restaurant → Order | One-to-Many | A restaurant fulfills many orders. |
| Restaurant → Review | One-to-Many | A restaurant receives many reviews. |
| Menu_Item → Item_Tags | One-to-Many | An item can have multiple dietary tags. |
| Order → Order_Item | One-to-Many | An order contains multiple line items. |
| Order → Payment | One-to-One | Each order has a single payment record. |
| Order → Delivery | One-to-One | Each order is assigned one delivery record. |

---

## Visual Schema Overview
