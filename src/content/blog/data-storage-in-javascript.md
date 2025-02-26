---
title: Client-side Data Storage in Javascript
description: Common approaches to store data on the client side with Javascript
date: 2023-09-06 15:46
draft: false
---
Working with Javascript, there're various ways to store and retrieve data on the client side. Gone are the days when cookies were our only option. Here I'll dig into the three main approaches: localStorage, sessionStorage, and IndexedDB.

## localStorage: The Persistent Workhorse

localStorage provides a simple key-value storage mechanism that persists across browser sessions. Its straightforward API makes it ideal for storing configuration preferences, theme choices, or other small bits of data that should survive page refreshes and browser restarts.

```javascript
// Storing data
localStorage.setItem('theme', 'dark');
localStorage.setItem('user', JSON.stringify({ id: 42, name: 'Alice' }));

// Retrieving data
const theme = localStorage.getItem('theme');
const user = JSON.parse(localStorage.getItem('user'));

// Removing data
localStorage.removeItem('theme');

// Clearing all data
localStorage.clear();
```

While localStorage is incredibly convenient, it comes with important limitations:
- Storage is typically capped at 5MB per origin
- It's synchronous, which can block the main thread
- Only strings are allowed (hence the need for `JSON.stringify`)
- No indexing or advanced query capabilities

Despite these constraints, localStorage remains my go-to solution for small, frequently accessed configuration data.

## sessionStorage: The Temporary Twin

sessionStorage functions nearly identically to localStorage, with one crucial difference: data persists only for the duration of a page session. Close the tab or window, and the data is wiped.

```javascript
// API is identical to localStorage
sessionStorage.setItem('currentView', 'dashboard');
const currentView = sessionStorage.getItem('currentView');
```

This makes sessionStorage perfect for:
- Form data that should survive page refreshes but not persist across sessions
- Temporary user preferences or view states
- Step data in multi-page workflows

Since it shares the same API as localStorage, switching between the two is trivial if your persistence requirements change.

## IndexedDB: The Full-Featured Database

When you need more horsepower, IndexedDB offers a complete client-side database with advanced features:

```javascript
// Opening a database
const request = indexedDB.open('MyAppDB', 1);

request.onupgradeneeded = (event) => {
  const db = event.target.result;
  
  // Create an object store with a key path
  const store = db.createObjectStore('users', { keyPath: 'id' });
  
  // Create indexes for faster queries
  store.createIndex('name', 'name', { unique: false });
  store.createIndex('email', 'email', { unique: true });
};

// Adding data
request.onsuccess = (event) => {
  const db = event.target.result;
  const transaction = db.transaction(['users'], 'readwrite');
  const store = transaction.objectStore('users');
  
  store.add({
    id: 1,
    name: 'John Doe',
    email: 'john@example.com',
    lastActive: new Date()
  });
  
  transaction.oncomplete = () => {
    console.log('Transaction completed');
  };
};
```

IndexedDB provides:
- Asynchronous API (won't block the main thread)
- Storage for complex JavaScript objects (no stringification needed)
- Indexes for optimized queries
- Transaction support for data integrity
- Much larger storage limits (typically 50-100MB or more)

The trade-off is complexity. IndexedDB's API is verbose and callback-heavy, making it harder to work with directly. This is why many developers opt for wrapper libraries like Dexie.js or IDB to simplify interactions:

```javascript
// Using Dexie.js wrapper
const db = new Dexie('MyAppDB');
db.version(1).stores({
  users: 'id, name, email'
});

// Much cleaner API
await db.users.add({
  id: 1,
  name: 'John Doe',
  email: 'john@example.com'
});

const johnsEmail = await db.users.get(1).email;
```

## When to Use Which?

My rule of thumb:

1. **localStorage**: For small configuration data, user preferences, or cached content that should survive browser restarts (< 100KB).

2. **sessionStorage**: For temporary state that should only exist during the current session, like form data or wizard steps.

3. **IndexedDB**: For structured data, large datasets, binary files (like images), or when you need to query/filter your data.

Additionally, it's essential to remember that all client-side storage methods have limits and can be cleared by users, so we should always implement fallbacks:

```javascript
function savePreference(key, value) {
  try {
    localStorage.setItem(key, value);
    return true;
  } catch (e) {
    // Likely a QuotaExceededError
    console.warn('Storage quota exceeded. Preference not saved.');
    
    // Maybe clear old data or use alternative storage
    return false;
  }
}
```

The best storage solution is always the one that meets your specific requirements while providing the best possible user experience. Don't overcomplicate things by using IndexedDB when localStorage would suffice, but also don't limit yourself to simple storage when your data needs demand more sophisticated solutions.

---

### References

- [LocalStorage and SessionStorage](https://javascript.info/localstorage)
- [IndexedDB](https://javascript.info/indexeddb)