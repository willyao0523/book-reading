### Optimize your interface for mobile

Designers should use these device-native features where possible, and not re- invent the wheel each time.

Ensure navigation bars use the host OS style so that users can feel instantly familiar and at home, not wasting valuable cognitive load on working out a new navigation system.

Accidental taps are among the most frustrating of mobile UX errors, and they can often be avoided by simply making buttons usable at mobile scale and *not simply copying the desktop interface and scaling it down*.

- **Information entry**
  - When the user finishes entering a selection, perhaps a drop-down select or a toggle, move them to the next field automatically
  - Split long forms up into sections so the user only has to navigate a few fields at a time, saving their entries as they go

Responsive design allows the page styling to detect the viewport size and adapt accordingly. All modern frontend frameworks come with sensible defaults built- in for page layout and control sizing. It’s almost always better to trust these defaults than to try to build your own responsive rules from scratch.

One example is scrolling—users are very happy to scroll vertically through pages of content, newsfeeds, and lists of items. Horizontal scrolling, however, is a big problem on mobile; it feels unnatural because of the limited width of the viewport and, even worse, it can interfere with the “back” gesture on most modern mobile browsers. Avoid horizontal scrolling.