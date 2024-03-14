# Requirements for Product Catalog LLD

This document outlines the requirements for the Low-Level Design (LLD) of a product catalog system.

## Functional Requirements:

### Product Information Management:

- Store essential product information, including:
  - Name (string)
  - Description (text)
  - Price (decimal)
  - Currency (string) (optional)
  - Availability (boolean) (optional)
  - Stock level (integer) (optional)
- Allow adding, editing, and deleting product information.

### Image Management:

- Store multiple images (URLs or references) for each product.
- Support different image sizes and formats (optional).

### Category Management:

- Define product categories with hierarchical structures (subcategories).
- Assign products to one or more categories.
- Allow adding, editing, and deleting categories.

### Filtering:

- Enable filtering of products based on various criteria:
  - Category
  - Price range
  - Availability
  - Keyword search in name or description (optional)
  - Other attributes (customizable, optional)


### Entities:

Below are the entities derived from the provided requirements along with an ER diagram:

1. **Product**:
   - Attributes:
     - id (Primary Key)
     - name (String)
     - description (Text)
     - price (Decimal)
     - availability (Boolean)
   - Relationships: None

2. **Category**:
   - Attributes:
     - id (Primary Key)
     - name (String)
   - Relationships: None

3. **Subcategory**:
   - Attributes:
     - id (Primary Key)
     - name (String)
   - Relationships: None

4. **ProductAssociation**:
   - Attributes:
     - id (Primary Key)
     - product_id (Foreign Key)
     - category_id (Foreign Key)
     - subcategory_id (Foreign Key)
   - Relationships:
     - Many-to-One relationship with Product
     - Many-to-One relationship with Category
     - Many-to-One relationship with Subcategory

5. **Stock**:
   - Attributes:
     - id (Primary Key)
     - product_association_id (Foreign Key)
     - quantity (Integer)
   - Relationships:
     - One-to-One relationship with ProductAssociation

6. **Image**:
   - Attributes:
     - id (Primary Key)
     - product_association_id (Foreign Key)
     - format (String)
     - size (String)
     - url (String)
   - Relationships:
     - One-to-One relationship with ProductAssociation

7. **Review**:
   - Attributes:
     - id (Primary Key)
     - product_association_id (Foreign Key)
     - user_id (Foreign Key)
     - review (Text)
     - timestamp (DateTime)
   - Relationships:
     - Many-to-One relationship with ProductAssociation
     - Many-to-One relationship with User

### ER Diagram:

```
                    +------------+                 +------------+
                    |  Category  |                 |  Subcategory|
                    +------------+                 +------------+
                          |                              |
                          |                              |
                          v                              v
                    +------------+   1    M  +---------------------+
                    |ProductAssoc|-----------|       Product       |
                    +------------+           +---------------------+
                          |                              |
                          |                              |
                    M     |     1                        |     M
          +-------------+|+-------------+       +---------------------+
          |    Stock    ||    Image    |       |       Review        |
          +-------------++-------------+       +---------------------+
```

This ER diagram illustrates the relationships between the entities. Each product can be associated with one or more categories and subcategories. Additionally, a product can have stock information, multiple images, and reviews.
