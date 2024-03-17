# Cart Service

## Functional Requirements

1. **User Authentication and Authorization**:
   - Users should be able to authenticate and log in to their accounts to access their shopping carts.
   - The system should support different user roles (e.g., regular users, administrators) with appropriate permissions.

2. **Session Management**:
   - The system should maintain user sessions to track active shopping carts.
   - Users should be able to add items to their shopping cart and continue shopping without losing their cart contents.

3. **Cart Operations**:
   - Users should be able to add items to their shopping cart by specifying the item ID and quantity.
   - Users should be able to view the contents of their shopping cart, including item details and quantities.
   - Users should be able to update the quantity of items in their shopping cart or remove items from the cart.
   - Users should be able to clear their entire shopping cart or remove specific items from the cart.

4. **Item Management**:
   - The system should maintain a catalog of available items with details such as item ID, name, description, price, and availability.
   - Administrators should be able to add, edit, or remove items from the catalog.

5. **Inventory Management**:
   - The system should track the availability of items in inventory and prevent users from adding out-of-stock items to their shopping cart.
   - When users add items to their shopping cart, the system should decrement the available quantity of the item in the inventory.

6. **Checkout Process**:
   - Users should be able to proceed to checkout from their shopping cart to complete their purchase.
   - The checkout process should include options for users to provide shipping information, select payment methods, and review their order summary.
   - After successful checkout, the system should generate an order confirmation with a unique order ID and provide users with a receipt.

7. **Guest Checkout**:
   - The system should support guest checkout for users who do not have an account. Guest users should be able to add items to their shopping cart and complete the checkout process by providing their shipping and payment information.

8. **Order History**:
   - Users should have access to their order history, which includes details of past purchases, order statuses, and order tracking information.
   - Users should be able to view and track the status of their current orders, including estimated delivery dates and shipment tracking numbers.


## Entities
Based on the provided functional requirements, we can identify the following entities and their relationships in the shopping cart system:

1. **User**:
   - Attributes: UserID, Username, Password, Email, Role
   - Relationships:
     - One user can have multiple sessions (1-to-many).
     - One user can have multiple orders (1-to-many).
     - Users can be associated with different roles (e.g., regular user, administrator) (many-to-one).

2. **Session**:
   - Attributes: SessionID, UserID, StartTime, LastActivityTime
   - Relationships:
     - One session is associated with one user (many-to-one).
     - One session can have multiple cart items (1-to-many).

3. **CartItem**:
   - Attributes: CartItemID, SessionID, ItemID, Quantity
   - Relationships:
     - One cart item belongs to one session (many-to-one).
     - One cart item is associated with one item from the catalog (many-to-one).

4. **Item**:
   - Attributes: ItemID, Name, Description, Price, Availability
   - Relationships:
     - One item can be added to multiple cart items (1-to-many).
     - One item can have multiple inventory records (1-to-many).

5. **Inventory**:
   - Attributes: InventoryID, ItemID, Quantity
   - Relationships:
     - One inventory record is associated with one item (many-to-one).

6. **Order**:
   - Attributes: OrderID, UserID, OrderDate, Status, TotalAmount
   - Relationships:
     - One order belongs to one user (many-to-one).
     - One order can have multiple order items (1-to-many).

7. **OrderItem**:
   - Attributes: OrderItemID, OrderID, ItemID, Quantity, UnitPrice
   - Relationships:
     - One order item belongs to one order (many-to-one).
     - One order item is associated with one item from the catalog (many-to-one).

8. **ShippingInformation**:
   - Attributes: ShippingInfoID, UserID, Address, City, State, ZipCode, Country
   - Relationships:
     - One shipping information record belongs to one user (many-to-one).

9. **PaymentMethod**:
   - Attributes: PaymentMethodID, UserID, PaymentType, CardNumber, ExpiryDate, CVV
   - Relationships:
     - One payment method record belongs to one user (many-to-one).

## ER Diagram

      +------------+             +------------+            +------------+          +------------+
      |    User    |             |   Session  |            |   CartItem |          |    Item    |
      +------------+             +------------+            +------------+          +------------+
      | UserID     |----<|-------| SessionID  |            | CartItemID |          | ItemID     |
      | Username   |             | UserID     |            | SessionID  |-----<|----| Name       |
      | Password   |             | StartTime  |            | ItemID     |          | Description|
      | Email      |             | LastActivityTime      | Quantity   |          | Price      |
      | Role       |             +------------+            +------------+          | Availability |
      +------------+                                                              +------------+
          |                                                                                |
          | 1                                                                             1|
          |                                                                                |
      +------------+                                                              +------------+
      |  Inventory |                                                              |   Order    |
      +------------+                                                              +------------+
      | InventoryID|--------<|----------------------------------------------------| OrderID    |
      | ItemID     |                                                              | UserID     |
      | Quantity   |                                                              | OrderDate  |
      +------------+                                                              | Status     |
          |                                                                       | TotalAmount|
          | 1                                                                     1|
          |                                                                       |
      +------------+                                                              +------------+
      | OrderItem  |                                                              |ShippingInfo|
      +------------+                                                              +------------+
      | OrderItemID|--------<|----------------------------------------------------|ShippingInfoID|
      | OrderID    |                                                              | UserID       |
      | ItemID     |                                                              | Address      |
      | Quantity   |                                                              | City         |
      | UnitPrice  |                                                              | State        |
      +------------+                                                              | ZipCode      |
                                                                                   | Country      |
                                                                                   +-------------+
