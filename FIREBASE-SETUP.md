# Ambassadors Club — Firebase Production Setup

This package is configured for Firebase project `ambassadorswebsite`.

## 1. Firebase Authentication

In Firebase Console:

1. Open **Authentication**.
2. Open **Sign-in method**.
3. Enable **Email/Password**.
4. Save.
5. Under **Settings → Authorized domains**, make sure your local development host and your final GitHub Pages domain are authorized.

The website creates a matching Firestore document at `users/{uid}` when a member registers.

## 2. Firestore Database

Open **Firestore Database → Rules** and publish the complete contents of `firestore.rules` from this package.

Do not combine it with the old test-mode rules. The final catch-all rule intentionally denies every collection that has not been explicitly configured.

## 3. Admin account

The website determines administrator access from:

`users/{ADMIN_UID}`

with:

`role: "admin"`

The normal member signup flow cannot promote a user to admin.

To create/promote an administrator, use a trusted administrative method such as the Firebase Console or a secured server/Admin SDK. Do not expose service-account credentials in the website.

## 4. Collections used by the website

| Collection | Public read | Member write | Admin write | Purpose |
|---|---|---|---|---|
| `users` | No | Own profile only | Not required | Member accounts/profiles |
| `rooms/{room}/messages` | No | Own messages | Yes | General/Prayer/Lesson/Worship chat |
| `directConversations` | Participants only | Participants | Yes | Private one-to-one chat |
| `directConversations/{id}/messages` | Participants only | Participants | Yes | Private messages/media |
| `resources` | Yes | No | Yes | Sermons/resources |
| `siteContent` | Yes | No | Yes | Website text and branding settings |
| `leadership` | Yes | No | Yes | Leadership profiles |
| `pageTabs` | Yes | No | Yes | Extra Lessons/Curriculum/Sermons/Leadership tabs |
| `events` | Yes | No | Yes | Events and finished/upcoming status |

## 5. Private chat

The website creates deterministic conversation IDs from the two UIDs. A private conversation is stored at:

`directConversations/{conversationId}`

and messages at:

`directConversations/{conversationId}/messages/{messageId}`

The rules check the participant list on every read/write. Being signed in alone does not give access to another person's private conversation.

## 6. Cloudinary

The website uses Cloudinary for uploaded images, video and audio. Firebase Storage is not used by this website, so Firebase Storage rules do not need to be configured for these uploads.

Configured Cloudinary values in the site:

- Cloud name: `st5yljwq`
- Unsigned upload preset: `AmbassadorsWeb`

Never put a Cloudinary API secret or Firebase Admin SDK service-account key into `index.html`.

## 7. Website editor

Admin-only website editing uses:

- `siteContent/home`
- `siteContent/pages`
- `siteContent/branding` (when branding upload fields are enabled)

Only admins can create/update these documents.

## 8. Leadership

Admin can add, edit and delete leadership entries. Leader photos are uploaded to Cloudinary and the resulting URL is stored in `leadership/{leaderId}.photo`.

## 9. Events

Admin can create and delete events and toggle their `finished` state. Event images are uploaded to Cloudinary and stored in the event document.

## 10. Extra page tabs

Admin can create/delete tabs in `pageTabs`. Public users can read them but cannot modify them.

## 11. Deploying rules with Firebase CLI

If Firebase CLI is installed and you are logged in:

```bash
firebase login
firebase use ambassadorswebsite
firebase deploy --only firestore:rules,firestore:indexes
```

The included `.firebaserc` and `firebase.json` are already configured for this project.

## 12. Test before going live

Use two different member accounts and test:

1. Account A signs in.
2. Account A opens Chatroom → Members.
3. Account A chooses Account B and sends a private message.
4. Account B signs in and opens the same member conversation.
5. Account B replies.
6. Account C signs in and confirms that the private conversation is not visible to C.
7. Confirm public chat still works.
8. Confirm an admin can manage resources, leadership, events and page tabs.
9. Confirm a normal member cannot create/update/delete admin content.
10. Confirm browser console has no Firestore `permission-denied` errors.

## 13. Important security note

Firestore Security Rules are the actual protection for client-accessible Firestore data. UI checks such as hiding an Admin button are only convenience; they are not a security boundary.
