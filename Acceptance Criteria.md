# Acceptance Criteria: Restaurant Discovery & Ordering App

This document defines the functional validation requirements for the MVP. Each criterion follows the **Given-When-Then** format to ensure clear, testable conditions for development and quality assurance.

---

## Navigation & Discovery

| ID | Scenario | Prerequisite (Given) | Action (When) | Expected Outcome (Then) |
| :--- | :--- | :--- | :--- | :--- |
| **AC-01** | Automatic Location Detection | The user has granted location permissions. | The application launches. | The system displays a restaurant feed ordered by proximity to the user's current coordinates. |
| **AC-02** | Manual Location Fallback | The user has denied location permissions. | The application launches. | The system requests manual input (zip code or city name) to populate the restaurant list. |

---

## Filtering & Sorting

| ID | Scenario | Prerequisite (Given) | Action (When) | Expected Outcome (Then) |
| :--- | :--- | :--- | :--- | :--- |
| **AC-03** | Price Level Filtering | The user is viewing the restaurant discovery screen. | They select a specific price tier (e.g., "$", "$$", "$$$"). | Only venues matching the selected price category appear in the feed. |
| **AC-04** | Rating-Based Sorting | The user is browsing the restaurant list. | They activate the "Top Rated" sort option. | The list reorders dynamically, showing highest-rated establishments first. |
| **AC-05** | Multi-Filter Application | The user has selected multiple criteria (e.g., price tier + minimum rating). | The feed refreshes. | Restaurants displayed meet all active filter conditions simultaneously. |
| **AC-06** | Filter Reset | At least one filter is actively applied. | The user clicks the "Clear Filters" button. | All applied filters are removed, restoring the default proximity-based restaurant list. |

---

## Menu Browsing

| ID | Scenario | Prerequisite (Given) | Action (When) | Expected Outcome (Then) |
| :--- | :--- | :--- | :--- | :--- |
| **AC-07** | Item Information Display | The user has opened a restaurant's menu. | They inspect any menu item. | The following fields are clearly visible: item name, description, unit price, and total calorie count. |

---

## Cart Management

| ID | Scenario | Prerequisite (Given) | Action (When) | Expected Outcome (Then) |
| :--- | :--- | :--- | :--- | :--- |
| **AC-08** | Adding Items to Cart | The user is viewing a restaurant menu. | They tap "Add to Cart" on an item. | The cart badge/icon increments to reflect the new total item count. |
| **AC-09** | Quantity Adjustments | The user has at least one item in their cart. | They increase or decrease the quantity of an item. | Both the item subtotal (price) and item calorie subtotal update immediately. |
| **AC-10** | Item Removal | An item exists in the cart with a quantity of 1 or more. | The user reduces the quantity to zero. | The item disappears entirely from the cart summary. |

---

## Cart Summary & Calculations

| ID | Scenario | Prerequisite (Given) | Action (When) | Expected Outcome (Then) |
| :--- | :--- | :--- | :--- | :--- |
| **AC-11** | Total Price Aggregation | The user has added multiple items to the cart. | They navigate to the cart view. | The system displays an accurate cumulative total price. |
| **AC-12** | Total Calorie Aggregation | The user has added multiple items to the cart. | They navigate to the cart view. | The system displays an accurate cumulative total calorie count. |

---

## Checkout Process

| ID | Scenario | Prerequisite (Given) | Action (When) | Expected Outcome (Then) |
| :--- | :--- | :--- | :--- | :--- |
| **AC-13** | Empty Cart Block | The cart contains zero items. | The user attempts to proceed with checkout. | The checkout button is disabled, accompanied by an on-screen message: "Cart is empty." |
| **AC-14** | Order Finalization | The user has a populated cart with at least one item. | They complete the checkout process. | A confirmation screen appears showing: final total price, total calories, and a unique order reference number. |

---

## User Experience & Responsiveness

| ID | Scenario | Prerequisite (Given) | Action (When) | Expected Outcome (Then) |
| :--- | :--- | :--- | :--- | :--- |
| **AC-15** | Mobile Device Compatibility | The user accesses the app on a smartphone or tablet. | They navigate through all core screens (discovery, menu, cart, checkout). | All UI elements render correctly without requiring horizontal scrolling, maintaining usability on smaller viewports. |

---

## Summary

All acceptance criteria are designed to validate the core functional requirements of the MVP. Each test case is independent and should pass consistently across supported devices and network conditions.