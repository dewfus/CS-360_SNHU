# Event Tracker

**CS-360 - Mobile Architect & Programming**
**Southern New Hampshire University**

An Android app for keeping track of personal events and getting a reminder on the day one arrives. Built in Java.

## About the project

Event Tracker is aimed at people who want a short list of upcoming events without setting up an account or syncing to a calendar service. Everything is stored locally in SQLite, and the app works with no internet connection.

The requirements were to build a working login backed by a database, a grid display of user data with full create, read, update, and delete support, and a notification feature that requests SMS permission and keeps working whether the user grants it or denies it.

The user need behind all of that is pretty simple. Someone has a handful of things coming up, they want to write them down somewhere that will still have them next week, and they want a nudge on the right day. The app is built around that and not much else.

## Screens and features

Four screens carry the whole app. Login handles both signing in and creating an account, so a first-time user is never stuck at a wall asking for credentials they do not have yet. The events grid shows everything the signed-in user has saved, sorted soonest first, with an empty state that tells a new user what to do instead of showing them a blank page.

The add and edit screen is one screen doing two jobs, which keeps the flow shorter and means editing looks exactly like creating. Dates and times come from pickers rather than text fields, so there is no way to type an invalid date.

The notification settings screen is where reminders get turned on and where the SMS permission is requested. The request happens there rather than at first launch, so the prompt shows up right after the user has asked for the thing that needs it.

## Coding approach

I built it from the bottom up, since nothing above the database could be tested until the database existed. Schema first, then repository classes holding all the SQL, then the screens on top of those.

Keeping every query inside two repository classes turned out to be the decision that paid off most. No activity touches SQLite directly, so when something went wrong with the data there was exactly one place to look. That is a pattern I plan to reuse.

## Testing

Testing was mostly manual on the Android Emulator, run after each layer rather than saved for the end. After the schema I used the Database Inspector to confirm the tables and columns were right before building anything on top of them. After the login work I created an account, force stopped the app, and signed back in to prove the data actually persisted rather than just living in memory.

The permission feature got tested both ways on purpose. Once with SMS granted, then again with it revoked from system settings, checking that the app still loaded, still showed events, and delivered the reminder as a notification instead of failing.

That second pass is the one that matters. Testing what happens when everything goes right proves very little, and every real bug I found came from testing the path where the user says no.

## Where the challenge was

The permission fallback took the most thought. The requirement said the app should keep working if SMS is denied, and the easy reading of that is to disable the reminder feature and move on.

Instead I routed the alert through a notification when SMS is unavailable, so the user still gets reminded and only the delivery method changes. That meant writing the alert manager to decide on the channel at send time rather than assuming one, and it left the feature intact for a user who turned the permission down.

## What I am most proud of

The database layer. The schema is normalized with a foreign key tying events to their owner, foreign key enforcement is turned on explicitly since SQLite leaves it off by default, every query is parameterized, and the helper is a singleton built on the application context so a destroyed activity cannot leak the connection.

None of that is visible from the screen, which is sort of the point. It is the part of the app I would be comfortable having someone else read.

## Tech

- Java, Android SDK
- SQLite via `SQLiteOpenHelper`
- Material Components, RecyclerView with `GridLayoutManager`
- Minimum SDK 24 (Android 7.0)
