# Music Streaming Web App Schema

A relational database schema for a music streaming web application inspired by platforms like Spotify and Apple Music. The schema models users, subscriptions, artists, albums, songs, playlists, social features, and listening history.

<img width="1351" height="955" alt="Screenshot 2025-12-24 192732" src="https://github.com/user-attachments/assets/df770672-2ad0-4ac5-9226-9653e260e6fe" />


## Features

- User accounts with profiles and secure password storage (hash at application layer)
- Subscription plans (free/premium) and per-user subscriptions
- Music catalog: artists, albums, songs, and genres
- User playlists with ordered tracks
- Social graph: users following users and artists
- Listening history for analytics, recommendations, and recently played

## Entity overview

### users
Stores core account information.

- `id` (PK)
- `firstname`
- `lastname`
- `username`
- `email`
- `password` (hashed)

Used for authentication, playlist ownership, subscriptions, follows, and listening history.

### artists
Represents musical artists.

- `id` (PK)
- `name`
- `artist_photo` (bytes / URL)
- `about_info`
- `genres`

Artists are linked to albums and songs.

### albums
Represents albums in the catalog.

- `id` (PK)
- `artist_id` (FK → artists.id)
- `album_name`
- `genre`

Each album belongs to one artist and can contain many songs.

### songs
Represents individual tracks.

- `id` (PK)
- `song_name`
- `genre`
- `artist_id` (FK → artists.id)
- `album_id` (FK → albums.id)
- `length` (e.g., seconds)

Songs are referenced in playlists and listening history.

### playlists
User-created playlists.

- `id` (PK)
- `creator_id` (FK → users.id)
- `playlist_name`
- `is_public` (boolean)

Each playlist is owned by a single user and can be public or private.

### list_content
Join table between playlists and songs with ordering.

- `playlist_id` (FK → playlists.id)
- `song_id` (FK → songs.id)
- `added_at` (timestamp)
- `position` (integer)

Defines which songs are in a playlist and their order.

### listening_history
Tracks user listening activity.

- `id` (PK)
- `user_id` (FK → users.id)
- `song_id` (FK → songs.id)
- `played_at` (timestamp)
- `duration_played` (integer)
- `device` (text)

Supports features like recently played, stats, and recommendations.

### user_follows_artists
User ↔ artist follow relationships.

- `user_id` (FK → users.id)
- `artist_id` (FK → artists.id)
- `followed_at` (timestamp)

Enables feeds and notifications based on followed artists.

### users_follows_users
User ↔ user follow relationships.

- `follower_id` (FK → users.id)
- `following_id` (FK → users.id)
- `followed_at` (timestamp)

Enables social features such as sharing playlists and activity.

### plans
Subscription plan definitions.

- `plan_id` (PK)
- `plan_name`
- `price_monthly` (money)
- `price_yearly` (money)
- `is_active` (boolean)
- `created_at` (timestamp)

Defines tiers like free, standard, premium.

### subs
User subscriptions to plans.

- `sub_id` (PK)
- `user_id` (FK → users.id)
- `plan_id` (FK → plans.plan_id)
- `status` (e.g., active, canceled, past_due)
- `started_at` (timestamp)
- `expires_at` (timestamp)
- `cancelled_at` (timestamp, nullable)

Used for access control and billing logic.

## Relationships (high level)

- One `user` → many `playlists`
- One `playlist` → many `songs` through `list_content`
- One `artist` → many `albums` and `songs`
- One `album` → many `songs`
- One `user` → many `listening_history` records
- Many-to-many:
  - users ↔ artists via `user_follows_artists`
  - users ↔ users via `users_follows_users`
  - playlists ↔ songs via `list_content`
- One `plan` → many `subs`; one `user` → many `subs`

## Getting started

1. **Clone the repository**

