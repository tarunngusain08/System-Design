# User Service

## Functional Requirements:

1. **User Profile Management**:
   - Users should be able to create and manage their profiles.
   - Profile information includes name, bio, location, etc.
   - Users can update their profiles as needed.

2. **Connection Management**:
   - Users should be able to connect with other users, forming connections like friends or followers.
   - Users can view and manage their connections, including adding or removing connections.

## Entities:

Based on the provided functional requirements, we can identify the following entities:

1. **User**:
   - Attributes: UserID, Username, Password, Email, Name, Bio, Location
   - Relationships:
     - One user can have multiple connections (1-to-many).
     - One user can have multiple profiles (1-to-1).

2. **Connection**:
   - Attributes: ConnectionID, UserID, ConnectedUserID, ConnectionType
   - Relationships: None

## ER Diagram:

```
          +------------+       1    M       +------------+
          |    User    |-------------------| Connection |
          +------------+                   +------------+
          | UserID     |       M    1       | ConnectionID |
          | Username   |<|----<|------|----| UserID       |
          | Password   |                   | ConnectedUserID |
          | Email      |                   | ConnectionType |
          | Name       |
          | Bio        |
          | Location   |
          +------------+

```

## API Endpoints:

1. **User Profile Management**:
   - `/api/users/{userID}/profile` (GET, PUT): Endpoint to get or update user profile information.

2. **Connection Management**:
   - `/api/users/{userID}/connections` (GET): Endpoint to get a list of user connections.
   - `/api/users/{userID}/connections/add` (POST): Endpoint to add a new connection for the user.
   - `/api/users/{userID}/connections/remove` (POST): Endpoint to remove a connection for the user.
