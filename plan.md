# Implementation Plan

## 1. Database Schema Updates
- Update the `articles` table to support a list of co-authors:
  - Add a new field `co_author_ids` as an array of user IDs (or create a many-to-many relationship if needed).
- Add a new `editing_locks` table (or extend article model) to track which user is currently editing which article and when:
  - `article_id`, `user_id`, `locked_at`, `last_seen_at`.

## 2. Create Article Page
- Add a "Co-Authors" multi-select dropdown field to the Create Article form.
- Fetch the list of all users and populate the dropdown.
- On submission, send selected co-author IDs with the article creation request.

## 3. Edit Article Page
- Check if the current user is in the list of allowed editors (owner or co-author).
- Implement article locking:
  - When user opens editor, assign a lock in the DB with timestamp.
  - Every 30 seconds, update their "last seen" timestamp (keepalive).
- If another user has the article locked, show error.
- Allow unlock if:
  - User navigates away,
  - Saves article,
  - 5 minutes passed since last seen.

## 4. Frontend UI
- Show error message if locked.
- Optional: display who is currently editing.

## 5. Save Logic
- BASIC: Allow last saved version to overwrite.
- ADVANCED: Respect locking logic.

## 6. Tests & Screenshots
- Test case 1: Zolly creates article and adds John.
- Test case 2: John edits article.
- Test case 3: Zolly opens same article in incognito → gets blocked (ADVANCED).
