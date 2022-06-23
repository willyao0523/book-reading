### When a user refreshes a feed, move them to the last unread item

Imagine a list of news items; it’s likely that a user would read the list, then choose one or more news items to read, each time navigating back to the list view. Don’t simply reload the feed and put the user back to the start again, you monster!

Of course, *technically*, the feed may well have changed in the time it took the user to read the story, but if it keeps updating, it’s disorienting and difficult to use. Yes, this means additionally keeping track of where your user’s scroll position is, but it’s worth it for the usability benefit.

- Return users to the same place that they came from

- Don’t reload or refresh feeds while a user is using them

- Give the user an option to manually refresh the feed while they’re using it