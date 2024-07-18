# frontend_test
the sixth challenge that is about frontend test
# in the following there are some explanations on what was the problem and how it is solved and what changes were made:
# front-task

This template should help get you started developing with Vue 3 in Vite.

## Recommended IDE Setup

[VSCode](https://code.visualstudio.com/) + [Volar](https://marketplace.visualstudio.com/items?itemName=Vue.volar) (and disable Vetur).

## Customize configuration

See [Vite Configuration Reference](https://vitejs.dev/config/).

## Project Setup

```sh
npm install
```

### Compile and Hot-Reload for Development

```sh
npm run dev
```

### Compile and Minify for Production

```sh
npm run build
```

### Overview

This project is a Vue.js application that displays a list of doctor profiles. Each profile includes basic information such as name, email, description, and the number of likes. Users can sort the profiles by the number of likes in ascending or descending order and leave comments for each doctor.

### Problem Description

The primary issue in the application was that after sorting the profiles by likes, any comment added to a profile would sometimes appear under a different profile. This was due to the sorting functionality not maintaining the correct association between profiles and their comments.

### Solution

To resolve this issue, we needed to ensure that comments are consistently associated with the correct profiles, even after sorting. This was achieved by:

1. **Maintaining a `comments` array within each profile object.**
2. **Emitting an event when a comment is submitted to ensure it is added to the correct profile.**
3. **Updating the `ProfileCard` component to handle the submission of comments properly.**
4. **Handling the event in the `App.vue` file to update the correct profile with the new comment.**

### Changes Made

#### 1. `src/components/ProfileCard.vue`

**Before:**

- The `ProfileCard` component rendered the profile information and had a simple input field for comments without any binding or event handling.

**After:**

- Added a `comment` data property to bind the input field.
- Emitted an `add-comment` event when a comment is submitted.
- Displayed the list of comments for each profile.

```vue
<template>
  <div class="profile">
    <div class="card">
      <DoctorIcon class="avatar"/>
      <div class="data">
        <div class="profile-content">
          <div>
            <strong>{{ profile.name }}</strong>
            <a :href="profile.email" class="email">{{ profile.email }}</a>
          </div>
          <div class="description">{{ profile.description }}</div>
        </div>
        <div class="likes">
          <span class="likes-icon">&#10084;</span>
          <span class="likes-value">{{ profile.likes }}</span>
        </div>
      </div>
    </div>
    <div class="comment">
      <input 
        class="comment-input" 
        v-model="comment" 
        @keyup.enter="submitComment" 
        placeholder="Write your comment..."
      >
    </div>
    <div class="comments-list">
      <div v-for="(comment, index) in profile.comments" :key="index" class="comment-item">
        {{ comment }}
      </div>
    </div>
  </div>
</template>

<script>
import DoctorIcon from "./DoctorIcon.vue";

export default {
  name: "ProfileCard",
  components: {
    DoctorIcon
  },
  props: {
    profile: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      comment: ''
    };
  },
  methods: {
    submitComment() {
      this.$emit('add-comment', { profileId: this.profile.id, comment: this.comment });
      this.comment = '';
    }
  }
};
</script>

<style>
/* Existing styles */
</style>
```

#### 2. `src/App.vue`

**Before:**

- The `App.vue` file handled the sorting functionality and rendered the list of profiles using the `ProfileCard` component.
- It did not handle comments correctly, leading to comments being associated with the wrong profile after sorting.

**After:**

- Added an `addComment` method to handle the `add-comment` event emitted by the `ProfileCard` component.
- Modified the profiles data structure to include a `comments` array for each profile.
- Updated the sorting functionality to ensure it does not affect the association of comments with profiles.

```vue
<template>
  <div id="app">
    <div class="section">
      <p>Profiles List</p>
      <div class="flex-row">
        <label class="label" for="filter">Find profile:</label>
        <input class="input">
      </div>
      <div class="buttons">
        <button @click="sortAsc">&#9650; </button>
        <button @click="sortDesc"> &#9660;</button>
      </div>

      <ProfileCard
        v-for="(profile, index) in profiles"
        :key="profile.id"
        :profile="profile"
        class="profile"
        @add-comment="addComment"
      />
    </div>

    <div class="section">
      <p class="header">Add new profile</p>
      <div class="flex-row">
        <label class="label">Name:</label>
        <input class="input">
      </div>
      <div class="flex-row">
        <label class="label" for="filter">Email:</label>
        <input class="input">
      </div>
      <div class="flex-row">
        <label class="label">Specialisation:</label>
        <input class="input">
      </div>
      <button>Add</button>
    </div>
  </div>
</template>

<script>
import ProfileCard from "./components/ProfileCard.vue";

export default {
  name: "App",
  components: {
    ProfileCard
  },
  data() {
    return {
      profiles: [
        {
          id: 1,
          name: "Wojciech",
          email: "wojciech@poz.pl",
          description: "Anaesthesiologist",
          likes: 34,
          comments: []
        },
        {
          id: 2,
          name: "Maria",
          email: "maria@poz.pl",
          description: "Radiologist",
          likes: 28,
          comments: []
        },
        {
          id: 3,
          name: "Anna",
          email: "anna@poz.pl",
          description: "Surgeon",
          likes: 53,
          comments: []
        }
      ]
    };
  },
  methods: {
    sortAsc() {
      this.profiles.sort((a, b) => a.likes - b.likes);
    },
    sortDesc() {
      this.profiles.sort((a, b) => b.likes - a.likes);
    },
    addComment({ profileId, comment }) {
      const profile = this.profiles.find(profile => profile.id === profileId);
      if (profile) {
        profile.comments.push(comment);
      }
    }
  }
};
</script>

<style>
/* Existing styles */
</style>
```

### Conclusion

With these changes, the application now correctly associates comments with their respective profiles, regardless of the sorting order. This ensures a consistent and intuitive user experience.

### Files Changed

- `src/components/ProfileCard.vue`
  - Added a data property for the comment.
  - Emitted an `add-comment` event on comment submission.
  - Displayed the list of comments for each profile.

- `src/App.vue`
  - Added an `addComment` method to handle the `add-comment` event.
  - Updated the profiles data structure to include a `comments` array for each profile.
  - Ensured sorting does not affect the association of comments with profiles.

---
