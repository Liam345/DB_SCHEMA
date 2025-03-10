# Database Schema

This README explains the database structure options for our chat application. Your task is to choose a database and design a schema that suits the application's needs. Below, we'll introduce the two main options—Convex and Supabase—along with their strengths, limitations, and tools. You'll decide which one to use and justify your choice based on your understanding.

## Option 1: Convex (NoSQL)

**What It Is:** Convex is a NoSQL database we've used to set up a real-time database for our chat app. It's fast to get started with and works well for real-time features (e.g., live message updates).

**How Queries Work:** Queries are written in TypeScript, so if you're comfortable with JavaScript/TypeScript, this might feel familiar.

**Pros:**
- Quick setup for real-time functionality.
- Growing community with responsive developers who listen to feedback.

**Cons:**
- Lacks some advanced features, making complex queries harder to write.
- Authentication isn't fully developed yet, so we pair it with Clerk (an external authentication tool).

**Authentication:** We're using Clerk because Convex's built-in authentication is limited.

## Option 2: Supabase (SQL)

**What It Is:** Supabase is an SQL-based database with a larger, more established community. It's adding real-time features, which could work for our chat app.

**How Queries Work:** Queries use SQL, so you'll need some basic SQL knowledge (e.g., SELECT, JOIN, etc.).

**Pros:**
- It has a bigger community, and its userbase has been growing among startups
- Rich feature set, including strong built-in authentication (no need for Clerk).
- SQL is widely used, so there's lots of support and resources online.

**Cons:**
- Real-time features are newer and might not be as seamless as Convex's.
- Requires SQL familiarity, which could be a learning curve if you're new to it.

**Authentication:** Supabase has robust authentication built-in, so it's a standalone solution.

## Your Task

You and your team get to choose between Convex or Supabase (or suggest another option if you have a strong case!). Here's what we expect:

1. **Pick a Database:** Decide which database fits the chat app best.
2. **Design a Schema:** Create a database schema (e.g., tables or collections for users, messages, etc.) that supports the app's features (like sending messages, storing user info, etc.).
3. **Explain Your Choices:** Be ready to justify:
   - Why you chose your database (e.g., real-time needs, ease of use, team skills).
   - Why your schema makes sense (e.g., how it organizes data efficiently).

Feel free to explore both options, research online, or even try small tests to see what works. The goal is to understand your tools and make smart decisions.

**Below is the Convex Schema for our existing web application for your reference:**

## Tables Overview

We have 4 tables:

- `users`
- `requests`
- `friends`
- `messages`

### Users

Stores user profile information and preferences.
In addition to the fields listed below, Convex will also automatically add \_id and \_creationTime fields to all the tables created. See [Convex Schema](https://docs.convex.dev/database/schemas) for more information.

| Column              | Type                | Description                                      |
| ------------------- | ------------------- | ------------------------------------------------ |
| username            | string              | User's display name                              |
| imageUrl / storageId| string              | Profile picture URL / Image Storage Id           |
| clerkId / authId    | string              | Authentication ID                       |
| email               | string              | User's email address                             |
| currentLocation     | object (optional)   | User's current location (city, state, country)   |
| destinationLocation | object (optional)   | User's target destination (city, state, country) |
| studyField          | string (optional)   | Field of study                                   |
| studyLevel          | string (optional)   | Academic level                                   |
| university          | string (optional)   | Institution name                                 |
| languages           | string[] (optional) | Languages spoken                                 |

**Indexes:**

- `by_id` : Lookup by id
- `by_email`: Quick lookup by email
- `by_clerkId`: Authentication lookup

### Requests

Manages connection requests between users.

| Column   | Type (users) | Description                                                     |
| -------- | ------------ | --------------------------------------------------------------- |
| senderId | ID           | User initiating the request                                     |
|receiverId| ID           | User receiving the request                                      |
| status   | string       | Request status either of ['pending', 'accepted', or 'declined'] |

**Reason for status column:** Avoid redundancy. No need to remove or move rows when a request is accepted or declined. This simplifies auditing and debugging.

**Indexes:**

- `by_receiverId`: Filter requests by receiver
- `by_senderId`: Filter requests sent

### Friends

Maintains friendship relationships between users.

| Column   | Type (users) | Description              |
| -------- | ------------ | ------------------------ |
| userId   | ID           | Reference to first user  |
| friendId | ID           | Reference to second user |

Instead of using a single row to represent a friendship, we store each friendship as two rows, one for each user.

**Example:** A friendship between users 1 and 2 is represented by these two rows:

- Row 1: {\_id: 1, userId: 1, friendId: 2, createdTime: '2025-01-01'}
- Row 2: {\_id: 2, userId: 2, friendId: 1, createdTime: '2025-01-01'}

**Indexes:**

- `by_userId`: Filter friends by user

### Messages

Stores communication between users.

| Column     | Type (users)      | Description                     |
| ---------- | ----------------- | ------------------------------- |
| senderId   | ID                | Message sender                  |
| receiverId | ID                | Message recipient               |
| type       | string            | Message type                    |
| content    | string[]          | Message content                 |
| readAt     | string (optional) | Timestamp when message was read |

**Indexes:**

- `by_receiver_sender`: Filter conversations between users

