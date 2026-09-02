# Admin website editor setup

The updated website adds:
- Admin editing for the Home page content.
- Admin editing for Lessons, Curriculum, Sermons and Leadership page headings/intros.
- A Leadership page with add/edit/delete leadership profiles.
- All editable site content is stored in Firestore.

## Firestore rules
Add these matches inside `service cloud.firestore` before the final catch-all rule:

```text
match /siteContent/{docId} {
  allow read: if true;
  allow create, update: if request.auth != null
    && get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'admin';
  allow delete: if false;
}

match /leadership/{leaderId} {
  allow read: if true;
  allow create, update, delete: if request.auth != null
    && get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'admin';
}
```

Keep the existing `resources` and `users` rules.

## Private member-to-member chat

The Chatroom now includes a **Members** area for private one-to-one conversations.

- Open **Chatroom → Members**.
- Choose another registered member and press **Message**.
- A private conversation is created under `directConversations/{conversationId}`.
- Each conversation stores exactly two member UIDs in `participants`.
- Private messages are stored under `directConversations/{conversationId}/messages`.
- The Members screen also shows your existing private conversations.
- Private conversation reads/writes are restricted by Firestore Security Rules so only the two participants can access them.

### Important: deploy the updated Firestore rules

The ZIP includes `firestore.rules`. In Firebase Console, open **Firestore Database → Rules**, replace your current rules with the contents of that file, and click **Publish**. The private-chat rules are necessary because Firestore rules do not automatically carry from a parent document to its subcollections. See Firebase's documentation on hierarchical data and security rules: https://firebase.google.com/docs/firestore/security/rules-structure
