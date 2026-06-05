# SocialMedia n8n Workflow

## Overview

This n8n workflow provides a unified form-driven system for publishing content to multiple social media platforms:

- LinkedIn
- Mastodon
- Bluesky

The workflow supports:

- Text posts
- LinkedIn article posts
- Image uploads
- Basic media handling
- Bluesky hashtag facet generation
- Validation for platform character limits

<img width="1199" height="762" alt="Screenshot from 2026-06-04 23-53-30" src="https://github.com/user-attachments/assets/e96aff6b-f8ec-4732-aa7a-96c8b96e5ec1" />

---
## Recognition

- Setting up n8n with LinkedIn was largely based on the video by user Lakshit Ukani | AI Automation on Youtube. Check out https://www.youtube.com/watch?v=OUrkh4VRSeA
- Setting up n8n with Mastodon was based on the video by user Dalandan Studio on Youtube. Check out https://www.youtube.com/watch?v=Vb4TMKjr2_g
- Setting up n8n with Bluesky was largely based on a video by a user on Youtube but can't find their video anymore... British guy with not much hair :)

## Features

### Multi-platform Posting

Users can select one or more target networks:

- Bluesky
- LinkedIn
- Mastodon

### Media Support

Supported media types:

- PNG
- JPG / JPEG
- BMP
- WEBP
- MP4

### Validation

The workflow validates:

- Character limits per platform
- LinkedIn article compatibility
- Required URL for LinkedIn article posts
- Media presence

### Bluesky Hashtag Facets

Automatically converts hashtags into Bluesky rich-text facets using UTF-8 byte offsets.

---

# Workflow Structure

## Main Flow

1. User submits content via form
2. Validation runs
3. Workflow branches by target network
4. Posts are published to selected platforms

---

# Nodes

## Form Trigger

### `PostForm`

Collects:

| Field | Type |
|---|---|
| PostContent | Textarea |
| PostType | Dropdown |
| TargetNetworks | Checkbox |
| PostMedia | File Upload |
| URLinPost | Text |

### Post Types

- Standard
- LinkedInArticle

---

## Validation

### `FormValidation`

Checks:

- Bluesky limit: 300 chars
- LinkedIn limit: 3000 chars
- Mastodon limit: 500 chars
- LinkedIn article compatibility
- File count

Returns validation flags used later in the workflow.

---

## Validation Gate

### `isValidationOK`

Routes:

- Valid posts → social network handlers
- Invalid posts → return form

---

# LinkedIn Flow

## Nodes

### `TargetSocialNetworkisLinkedin`

Checks whether LinkedIn was selected.

### `hasMedia1`

Determines whether media is attached.

### `MediaSwitch`

Routes media posts based on MIME type.

Outputs:

- image
- video

### `nonMediaSwitch`

Routes non-media posts.

Outputs:

- articlePost
- standardPost

### `Create a post`

Publishes LinkedIn image posts.

### `PostText`

Publishes standard text posts.

### `PostArticle`

Publishes article link posts.

---

# Mastodon Flow

## Nodes

### `TargetSocialNetworkisMastodon`

Checks whether Mastodon was selected.

### `hasMedia`

Determines whether media exists.

### `Mastodon-Media-Upload`

Uploads media to Mastodon.

Endpoint:

```text
POST /api/v2/media
```

### `Mastodon-Post-with-Media`

Publishes status with uploaded media.

Endpoint:

```text
POST /api/v1/statuses
```

### `Mastodon-Post-Simple`

Publishes text-only status.

---

# Bluesky Flow

## Nodes

### `TargetSocialNetworkisBluesky`

Checks whether Bluesky was selected.

### `CreateBSkySession`

Creates Bluesky session.

Endpoint:

```text
POST /xrpc/com.atproto.server.createSession
```

### `hashtagFacets`

Extracts hashtags and converts them into Bluesky facets.

### `BSkyPost`

Publishes post to Bluesky.

Endpoint:

```text
POST /xrpc/com.atproto.repo.createRecord
```

---

# Credentials Required

The workflow expects configured credentials for:

| Platform | Credential Type |
|---|---|
| LinkedIn | OAuth2 |
| Mastodon | OAuth2 |
| Bluesky | HTTP Custom Auth |

---

# Media Notes

Current limitations:

- Mastodon upload flow assumes a single file
- LinkedIn video publishing is incomplete
- Bluesky media publishing is not implemented

---

# Security Notes

The exported workflow does not contain plaintext secrets.

However, before sharing publicly you should remove:

- Credential IDs
- Webhook IDs
- Instance IDs
- Localhost URLs
- Internal identifiers

---

# Suggested Improvements

## Validation

Add checks for:

- File size
- Number of uploads
- MIME type restrictions

## Media Handling

Enhance support for:

- Multiple Mastodon uploads
- Bluesky image uploads
- LinkedIn video publishing

## Error Handling

Add:

- Retry logic
- Error notifications
- Failed-post logging

---

# Platform Limits

| Platform | Character Limit |
|---|---|
| Bluesky | 300 |
| LinkedIn | 3000 |
| Mastodon | 500 |

---

# Requirements

- n8n
- LinkedIn API access
- Mastodon OAuth app
- Bluesky credentials

---

# Importing the Workflow

1. Open n8n
2. Import the workflow JSON
3. Configure credentials
4. Activate the workflow
5. Test using the generated form

---

# Notes

This workflow is designed as a reusable social publishing hub for lightweight cross-posting workflows.
