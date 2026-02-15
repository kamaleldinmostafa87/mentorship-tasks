# User Perspective

### Authentication

- Normal register
- Register using OAuth (google,..ets)
- Handle case if the user close the app after register form

### Fill simple form

- Name (required)
- Phone number (required)

### Filter the restaurants

- Customer can filter the restaurant based on the location
- Customer can filter the restaurant based on the rate

### Browsing The generated restaurant

- Each restaurant has logo and name
- Each restaurant has its menu

### In the restaurant details page

- View the comments if there are exist
- Availability write a comment & Edit & Delete it
- Availability increase or decrease the stars of the restaurant that reflect to its rate
- View the menu of the restaurant

### Picking an order

- Pick an order
- Handle out of stock scenario

### Checkout Functions

- Address form
- Checkout form
- Function of sending OTP from the user bank to confirm the checkout process
- Notify the user with the expect time of arriving the delivery to the typed (saved) address
- State of the order (pending, accepted, rejected)

---

# Restaurant Perspective

### Authentication

- Normal register
- Register using OAuth (google,..ets)

### Fill simple form

- Name of the restaurant (required)
- Phone number (required)
- Location of the restaurant (required)

### Restaurant functions

- View the comments & rate if there are exist
- Each restaurant can upload, update, delete its menu
- View number of customers
- View the orders
- Generate approximate time that will appear at the customer screen to deliver the order to the customer
- State of the order (pending, accepted, rejected)

### Out of stock feature

- Handle out of stock scenario

---

# API's Signatures

## Cart Management

| Signature                        | Input                  | Output                                                                            |
| -------------------------------- | ---------------------- | --------------------------------------------------------------------------------- |
| /api/v1/cart/add                 | {customerId,cartItems} | 200 created<br>400 bad request (wrong in data)<br>409 conflict with existing cart |
| /api/v1/cart/remove/{cartItemId} | -                      | 200 updated                                                                       |
| /api/v1/cart/update/{cartItemId} | {cartItems[]}          |                                                                                   |
| /api/v1/cart/get/{customerId}    | -                      |                                                                                   |

## Order Management

| Signature                        | Input                      | Output                                                                             |
| -------------------------------- | -------------------------- | ---------------------------------------------------------------------------------- |
| /api/v1/order/add                | {customerId, cartItems []} | 201 created <br>400 bad request (wrong in data)<br>409 conflict with existing cart |
| /api/v1/order/remove/{orderId}   | -                          | 200                                                                                |
| /api/v1/cart/update/{cartItemId} | {cartItems[]}              | 200 OK<br>400 bad request<br>404 not found this order<br>403 Forbidden             |
| /api/v1/cart/get/{customerId}    | -                          | 200 OK<br>400 bad request<br>404 not found                                         |

## Login

| Signature                    | Input         | Output                                                       |
| ---------------------------- | ------------- | ------------------------------------------------------------ |
| /api/v1/user/register        | {user_info[]} | 201 created <br>400 bad request (wrong in data)              |
| /api/v1/user/remove/{userId} | -             | 200 OK<br>404 not found                                      |
| /api/v1/user/update/{userId} | {user_info[]} | 200 OK<br>400 bad request<br>404 not found <br>403 Forbidden |
| /api/v1/user/login           | {user_info[]} | 200 OK<br>400 bad request<br>404 not found                   |
|                              |               |                                                              |

## Restaurant Management

| Signature                                   | Input                                                | Output                                                             |
| ------------------------------------------- | ---------------------------------------------------- | ------------------------------------------------------------------ |
| `POST /api/v1/restaurants`                  | `{ name, address, phone, cuisineType, openingHours}` | **201 Created** <br>400 Bad Request <br>409 Conflict               |
| `GET /api/v1/restaurants`                   | `-`                                                  | 200 OK <br>400 Bad Request                                         |
| `GET /api/v1/restaurants/{restaurantId}`    | `-`                                                  | 200 OK <br>404 Not Found                                           |
| `PUT /api/v1/restaurants/{restaurantId}`    | `{ name, address, phone, cuisineType }`              | 200 OK <br>400 Bad Request <br>404 Not Found <br>**403 Forbidden** |
| `DELETE /api/v1/restaurants/{restaurantId}` | `-`                                                  | 200 OK <br>404 Not Found <br>**403 Forbidden**                     |

## Menu Management

| Signature                                                                | Input                                 | Output                                                                  |
| ------------------------------------------------------------------------ | ------------------------------------- | ----------------------------------------------------------------------- |
| `POST /api/v1/restaurants/menu/categories/{restaurantId}`                | `{ name, description, displayOrder }` | 201 Created <br>400 Bad Request <br>404 Not Found <br>**403 Forbidden** |
| `GET /api/v1/restaurants/menu/categories/{restaurantId}`                 | `-`                                   | 200 OK <br>404 Not Found                                                |
| `PUT /api/v1/restaurants/{restaurantId}/menu/categories/{categoryId}`    | `{ name, description, displayOrder }` | 200 OK <br>400 Bad Request <br>404 Not Found <br>403 Forbidden          |
| `DELETE /api/v1/restaurants/menu/categories/{restaurantId}/{categoryId}` | `-`                                   | 200 OK <br>**409 Conflict**  <br>404 Not Found                          |

## Transaction Management

| Signature                                               | Input | Output                                                             |
| ------------------------------------------------------- | ----- | ------------------------------------------------------------------ |
| `GET /api/v1/transactions/{transactionId}/{customerId}` | -     | 200 Ok <br>400 Bad Request <br>404 Not Found <br>**403 Forbidden** |
| GET /api/v1/restaurants/{restaurantId}/transactions     |       | 200 Ok <br>400 Bad Request <br>404 Not Found <br>**403 Forbidden** |

## Payment Management

| Signature                                          | Input            | Output                                                             |
| -------------------------------------------------- | ---------------- | ------------------------------------------------------------------ |
| `GET /api/v1/payment/{transactionId}/{customerId}` | {card_details[]} | 200 Ok <br>400 Bad Request <br>404 Not Found <br>**403 Forbidden** |
| POST /api/v1/users/payment-methods/{userId}        |                  | 200 Ok                                                             |
| GET /api/v1/users/{userId}/payment-methods         |                  | 200 Ok                                                             |
| DELETE /api/v1/payment-methods/{methodId}          |                  | 200 Ok <br>404 NO FOUND                                            |
