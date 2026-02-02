# @minisquare/react-context

-   AlertsContext - Manages alerts
-   FirebaseAuthContext - Provides firebase's user object
-   MinHeightContext - Helps prevent blank vertical space

### Installation

#### npm

```bash
npm install @minisquare/react-context
```

#### Yarn

```bash
yarn add @minisquare/react-context
```

## AlertsContext

### Purpose

The AlertsContext provides a global way to add, remove, and clear alert messages across the app.

### API

The context exposes the following functions:

-   `addAlert(type: "success" | "info" | "warning" | "error", message: string): string`
    Adds a new alert and returns its ID. Alerts are automatically removed after 5 seconds.
-   `removeAlert(id: string): void`
    Removes a specific alert by its ID.
-   `clearAlerts(): void`
    Removes all alerts.

### Result

![Screenshot of the alerts context in use](https://cdn.usq.re/jack/alerts.png)

### Example usage

Wrap your app in the `AlertsProvider`.

```jsx
import { AlertsProvider } from "@minisquare/react-context"

const App = () => {
    return (
        <AlertsProvider>
            <YourContent />
        </AlertsProvider>
    )
}
```

Manage alerts with the `useAlerts` hook.

```jsx
import { useAlerts } from "@minisquare/react-context"

const Form = () => {
    const { addAlert, removeAlert } = useAlerts()

    const save = async ({ email, password }) => {
        if (!email || !password) {
            return addAlert("warning", "Please check your details.")
        }

        const alertId = addAlert("info", "Saving...")

        try {
            // ...save logic
            addAlert("success", "Saved successfully!")
        } catch (error) {
            addAlert("error", "Failed to save.")
        } finally {
            removeAlert(alertId)
        }
    }

    // ...form logic
}
```

## FirebaseAuthContext

### Purpose

The FirebaseAuthContext provides a global instance of `user` so that `onAuthStateChanged` is only called in one place.

### Example usage

Wrap your app in the `FirebaseAuthProvider` and pass it the initialized Firebase `auth` instance.

```jsx
import { initializeApp } from "firebase/app"
import { getAuth } from "firebase/auth"
import { FirebaseAuthProvider } from "@minisquare/react-context"

const firebaseConfig = {
    // ...your config
}

const app = initializeApp(firebaseConfig)
const auth = getAuth(app)

const App = () => {
    return (
        <FirebaseAuthProvider auth={auth}>
            <YourContent />
        </FirebaseAuthProvider>
    )
}
```

Access the current Firebase user with the `useFirebaseAuth` hook.

```jsx
import { useFirebaseAuth } from "@minisquare/react-context"

const Profile = () => {
    const { user } = useFirebaseAuth()

    if (!user) return <p>Please sign in.</p>

    return <p>Welcome, {user.displayName ?? user.email}</p>
}
```

## MinHeightContext

### Purpose

The MinHeightContext provides `minHeight`. Pass `ids` to the provider and `minHeight` will equal the window height minus the height of the elements with those ids. Setting this as the `minHeight` of the page's main content will prevent blank vertical space below the footer.

### Result

Without min height context, the footer is not always at the bottom of the page:

![Screenshot of a page without the min height context](https://cdn.usq.re/jack/min-height-without.png)

With min height context, the footer is kept at the bottom of the page:

![Screenshot of a page with the min height context](https://cdn.usq.re/jack/min-height-with.png)

### Example usage

Wrap your app in the `MinHeightProvider` and pass it the list of `ids`.

```jsx
import { MinHeightProvider } from "@minisquare/react-context"

const App = () => {
    return (
        <MinHeightProvider ids={["header", "footer"]}>
            <header id="header">
                <p>Header content</p>
            </header>

            <YourContent />

            <footer id="footer">
                <p>Footer content</p>
            </footer>
        </MinHeightProvider>
    )
}
```

Use the hook to style the page's content.

```jsx
import { useMinHeight } from "@minisquare/react-context" 

const Home = () => {
    const { minHeight } = useMinHeight()

    return (
        <main className="home" style={{ minHeight }}>
            <p>Home content</p>
        </main>
    )
}
```

### Styles

The calculations will only work with certain css settings, such as below.

```css
* {
    box-sizing: border-box;
}

body,
h1,
p {
    margin: 0;
}
```
