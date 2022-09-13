### Wireframing and Screen Layout

#### **The Importance of Wireframing**

Wireframing is essential in UI design. It allows you to give structure to your ideas and materialize them. Wireframing exposes the rough edges in your thoughts early on so you can iterate them without time-wasting changes and revisions.

Diving directly into creating high-fidelity mockups that look and feel real might seem tempting at this stage, and for good reason. But you are going to start with wireframes as working in low-fidelity helps prevent wasting time and effort.

低保真开始减少浪费

Wireframing is particularly crucial when working in a cross-functional team because it lets key stakeholders get involved early in the ideation phase to discuss different approaches. It opens up the discussion about which direction to take and promotes collaboration. Your collaborators don’t need to know how a specific tool works to provide valuable input.

When you’re wireframing an app, it’s essential to keep customization and styling to a bare minimum. Wireframes need to be generic enough to allow for rapid iteration and exploration while still having critical building blocks in place to define the foundation of the core experience. This stage in design focuses more on function and less on form.

保持自定义和样式至小量；这个阶段主要focus在功能而不是形式

Establishing the core flow and functional structure with wireframes will allow you to modify and tune the app’s cosmetic aspects in the future. In the chapters that follow, you’ll build on top of the wireframe, adding the finer details and polish like establishing a palette and a typographic scale.

#### **Defining the App’s Concept**

Before you turn to the drawing board, it’s helpful to define the idea you’re working toward.

define想法

In this case, you’re building a movie tracking app that lets users curate a list of movies they want to watch and gives details about the movie, like the cast, genre, duration, ratings and so on.

From the intent above, you can break down the app into a few key features. It should:

- Display a list of movies.
- Show the movie details.
- Let the user bookmark/favorite a movie.

Breaking the broad idea down into smaller features helps you focus on them one at a time without worrying about the big picture.

把一个大的想法分解到较小的feature上

Now that you have defined these key features, you’ll tackle them one by one. As you work, you’ll keep the details and finish in the wireframes low-fidelity so you can change things around quickly and create multiple iterations.

Launch Figma and create a new file. Add a new frame to the canvas by pressing **F** and selecting the **iPhone 11 Pro Max** frame. Name the file **cinematic-wireframe** by clicking on an empty spot in the canvas and then clicking on the file name in the toolbar.

#### **Designing the Movies List**

For the first iteration, you’ll create a conventional list, like the ones you’ve seen in most of the apps you use. The list item will have five components:

- Movie poster
- Movie name
- Metadata (release year, director, etc.)
- Genres
- Rating

Start building this screen by adding a **414×224** rectangle (R) for the movie list item container, then removing the fill and adding a **black** stroke. Align the rectangle to the top and left of the frame by either moving it manually or by setting the **X** and **Y** fields to **0** in the properties panel. Finally, name the frame **container**.

Now, add another rectangle (R) for the movie poster measuring **125×192**. Remove the fill, and add a **black** stroke. Switch to the **Line Shape Tool (L)** and add two diagonal lines. Align the ends of each line to the corners of the rectangle to create an x. Group the rectangle and diagonal lines and name the group **movie-image**.

Position **movie-image** inside the **container** on the left side. Give it a margin of **16** on the left, top, and bottom from the sides of the **container** by moving it manually or by setting the **X** and **Y** fields to **16** in the properties panel. Your list item should look like this:

#### **Adding the Movie Name**

For the movie name, add a text layer (T) with **SpiderMan Far From Home**, a font size of **24** and the font **Roboto-Regular**. Name this layer **movie-name**, align it to the left of **movie-image** with a left margin of **32**, a right margin of **32** and a top margin of **16**.

#### **Adding the Metadata**

For the next row, which contains the movie metadata, add a text layer (T) with **2019** to denote the movie release year. Give it a size of **14** and use the font **Roboto** with a **Regular** weight.

Then, duplicate the release year layer and change the text to **1h 42 min** denoting the movie’s duration. Select both these text layers and create an Auto Layout (Shift-A).

In the Auto Layout Properties, select **Horizontal** direction with a spacing of **12**. Add another text layer (T) in the layout with **Cathy Yan**, denoting the director.

The Auto Layout with the three text layers should look like this:

Name the Auto Layout **movie-info** and place it below **movie-name** with a top margin of **16**.

#### **Adding Genres**

For the genres row, create a rectangle (R) measuring **51×18** with a default fill. Now, add a text layer (T) on top of the rectangle with the text set to **Action**, a font size of **12** and the font **Roboto-Regular**. Align the text to the rectangle **horizontally** and **vertically**. With both layers selected, change the constraints to **center**.

Next, with both layers selected, create an Auto Layout (Shift-A) and name it **genre**. This makes the background rectangle resize according to the text width.

Duplicate **genre** and change the text to **Drama**. Place the duplicated genre to the right of the **Action** genre with a margin of **8**. Now, select both genre auto layouts and create another Auto Layout (Shift-A) to create a row. Name this auto layout **genres**. Duplicate one of the genre auto layouts and change the text to **Fiction**. Notice that the margin and constraints stay consistent.

Place the **genres** row below the **movie-info** row with a top margin of **16**.

#### **Adding Ratings**

For the **rating** row, select the **Star** from the Shape Tools menu.

Add a **20×20** Star with a fill color of **black**. Duplicate this layer five times and, with all five star layers selected, use Auto Layout (Shift-A) to create a row of five stars. Select **Horizontal** alignment and add a margin of **8** between each star. Change the fill color of the fifth star to **gray**. Finally, name this auto layout **rating**.

Place this row below the genres layer with a top margin of **16**.

Group all the layers in your frame and name it **movie-list-item**. Duplicate the movie-list-item four times and, with all four groups selected, use Auto Layout (Shift- A) and name the result **movies-list**. Inside each movie-list item, change the name of the movie accordingly.

This list can be a category of movies like trending, top-rated, etc. In this app, you’ll display two types of movies along with a user-curated favorites section. You can reuse the list across the different sections to a great extent, but you’ll need a way to navigate.

#### **Creating the Navigation**

Start by adding a **414×52** rectangle (R) with a default fill and a **black** stroke to the frame. Align it to the bottom of the frame and place it on top of the movies list. This rectangle will serve as a container for the navigation destinations.

On top of this rectangle, add a **36×36** ellipse (O) with a fill of **#7A7575**. Align the ellipse vertically with the rectangle and place it to the rectangle’s left with a margin of **29**.

Duplicate the ellipse and align the new ellipse horizontally to the rectangle, placing it in the center. Select the two ellipses and use Auto Layout (Shift-A).

Duplicate the ellipse one more time, giving you three ellipses representing the three destinations. Because the first two ellipses are using Auto Layout the third ellipse should automatically be positioned to the right side of the navigation container.

Group the rectangle and the ellipses and name the group **bottom-navigation**.

Great job creating your first wireframe! Even though this screen isn’t fleshed out with real data, you can see a preliminary structure taking form.

Looking at the wireframe you just created, you’ll notice that certain elements will probably stay the same across different variations, for example:

- The movie poster element
- The rating row
- The genres row

You’d make the overall iteration much faster by extracting them so you can reuse them. It’s time to take a quick detour and learn about another powerful Figma feature.

#### **Components**

**Components** in Figma are UI elements that you can reuse across multiple design files. They allow you to create consistent designs and make changes quickly.

A great way to visualize this is to consider the rating row in your current wireframe. Imagine you’ve created ten different wireframes that show the ratings in the form of stars. Each of these iterations has seven movie cards. If you later decide to change the icon from a star to a heart, you’d have to make changes in 70 places! That’s a lot of wasted time and unnecessary changes.

This is where components shine. They let you create consistent elements once, then reuse them. When you make changes, you only need to modify them in one place and those changes will reflect across files.

只需要修改一处

You’ll try this out by converting the movie image, genre row, the rating row and the navigation bar into components.

#### **Creating your Components**

Before creating your components, take a moment to organize things by breaking them up on a different frame. Press **F** and create a new **Macbook Pro 14”** frame, which you’ll find under the **Desktop** category in the Frames menu. Name this frame **components**.

Now, copy one of the **movie-list-item** groups to the **components** frame, then right- click and select **Ungroup**. You can now freely move the individual elements that make up the list item around the canvas.

Duplicate the **movie-image** group, then click on the **Create Component** option from the toolbar on top.

Alternatively, you can right-click on the group and click **Create Component**.

Nothing has changed visually, but if you look at the Layers panel, the movie-image group now has a new icon shaped like four diamonds. This icon denotes that the group is now a component.

Repeat the same process for the genres and rating groups, duplicate them and clean up their placement on the canvas so it’s easy to differentiate between individual components on the frame.

Copy over the **bottom-navigation** group and create a component for it as well.

#### **Implementing Your Components**

Now, in the components canvas, rebuild the **movie-list-item** card by replacing the movie-image, genre, and rating groups with the components you just created.

To reuse a component, duplicate it by holding **Alt/Option** while clicking and dragging it, or click on the **Assets** option in the Layers panel to see a list of all your components, as shown below.

Drag and drop the components you select from the Assets panel into the canvas.

Once you’ve replaced the movie-image, genres, and rating groups with components, regroup the movie-list-item layers you ungrouped previously. Name this group **movie-list-item** and then turn it into a component.

Here’s how the component will look in the Layers panel.

Your canvas should now have five reusable components, including the movie-list- item, which is made up of smaller components. Here’s what your components frame should look like:

Now, it’s time to use the components you just created to clean up the wireframe. In the process, you’ll see how powerful they are.

#### **Trying out the Components**

Create a new **iPhone 11 Pro Max** frame (F) and name it **movie-list-wireframe**. Add a **bottom-navigation** component to this frame by dragging it from the Assets panel then align it to the bottom of the frame. Set the bottom navigation constraints to **Left** and **Bottom**.

Now, add four instances of the **movie-list-item** component to the frame. Change the movies’ names, and while selecting all four **movie-list-item** components, use Auto Layout (Shift-A). Name this auto layout **movie-list**.

Time for some magic! On the **components** frame, select the movie-image component and give it a fill of **#DAD7D7**. As soon as you add a fill to the main component, notice that all instances immediately reflect the change.

Now, select the individual genres in the genres component and give them a corner radius of **12**. You will notice that all genre rows now use a rounded rectangle.

Components are a powerful feature that makes iteration and revisions extremely fast. The next chapter goes into components in more depth and uses them far more extensively. If you like what you saw here in this brief introduction, you’ll enjoy working with components in the next chapter.

#### **Reviewing the Current List Implementation**

The list looks good, but before proceeding, it’s important to spend some time with the wireframe and see how it fits in the grand scheme of things. This is also a good time to refine your ideas and see if your strategy needs any course correction.

You started with the idea of building a movie tracking app that lets users curate a list of movies they want to watch and that gives details about the movie.

You then broke down the idea into the overarching goals of:

- Showing a list of movies.
- Showing details about a movie (synopsis, cast, etc.).
- Adding the ability to bookmark/favorite a movie.

The breakdown was a great starting point, but it leaves many unanswered questions about the app’s function.

- Are the movies sorted by release dates or ratings?
- Does the user have an option to modify the list using filters?
- What if the user has already seen a movie? How do we prevent showing the viewed movie again?
- Does the concept of favorites make sense in this app, or should we pivot to calling it **bookmarks**, or **wishlist**?
- Where do the ratings come from?
- Does the year and duration of a movie make sense to show on the card? How does that information influence the user’s decision to bookmark a movie?
- Is the information on the list enough for the user and if so, is a detail screen even required?

电影的排序？

用户是否可以使用过滤修改列表？

如果用户已经看了一个电影，我们怎么让看过的电影不显示？

喜欢某某这个概念在这个应用中是否成立，或者我们应该把这个当作书签或者愿望清单？

评分从哪里来？

电影年份和时长是否有必要显示在卡片上，信息如何影响用户收藏一个电影的决定？

列表的信息是否对用户足够，如果足够，详情页还需要吗？

These questions are difficult to answer because, while some are purely related to design and usability, others require input from the engineering teams and more detailed user research to figure out what might or might not work. Cross-team collaboration is vital to a successful app.

In an actual product development lifecycle, instead of assuming the user’s preferences and requirements, you’d involve those users in development via surveys and studies. Doing user research and feedback not only validates your assumptions but also provides valuable insights into how they perceive what you’ve built and whether it works for them.

It’s important to understand that wireframing isn’t the only iterative cycle in product development. While wireframing helps you realize the app’s core foundation, the final form it takes is heavily influenced by user feedback. This cycle of **iteration** **▸** **prototyping** **▸** **feedback** repeats with every new feature addition and redesign.

**iteration => prototyping => feedback**

#### **Making Some Decisions**

Since this chapter’s scope is focused on wireframing, you’ll just go over the questions above and think about the solutions so you can iterate over the design and continue making progress.

• **Are the movies sorted by release dates or rating?**

By default, the list is sorted by release date. This will make sure new releases show up at the top of the list.

• **Does the user have an option to modify the list using filters?**

The initial ideation phase didn’t include this feature, but it makes a lot of sense to allow users to filter the list based on their preferences.

过滤是有意义的

• **What if the user has already seen a movie? How do we prevent showing the viewed movie again?**

The user can curate a list of movies they want to watch. This curated list will be a separate section of the app. A bookmarked movie can be marked as seen, which will prevent it from appearing on the main list.

• **Does the concept of favorites make sense in this app or should we pivot to calling it bookmarks or wishlist?**

While the behavior is the same, calling the feature **Add to watchlist** makes more sense. It also provides a clear description of what it does, compared to the other options.

• **Where do the ratings come from?** 

The ratings come from an external source, like IMDB or Rotten Tomatoes. This is one of the decisions that the engineering and product teams have more influence over.

• **Does the year and duration of a movie make sense to show on the card? How does that information influence the user’s decision to bookmark a movie?**

The release year influences the sorting order, and the duration might be important in certain circumstances. If you have a two-hour flight coming up, you’d plan to watch a movie that fits your flight duration. This is an assumption at this point, but user feedback would help validate and refine the idea.

• **Is the information on the list enough for the user and if so, is a detail screen even required?**

The list shows information at a glance, but a user’s decision to watch a movie also depends on many other factors. Some users like seeing the trailer before deciding, while others are picky about the actors. Some even like to spoil it for themselves by reading the plot.

These richer pieces of information are tough to squeeze inside a list, so a detail screen makes sense. The detail screen also gives you space to promote sponsored content tailored to the user’s preferences, if the need arises.

Now that you have some actionable answers, revisit the movie list screen. As of now, the only piece missing is an option to filter the list.

#### **Filtering the List**

Your next step is to add a button to surface the filter options.

In **movie-list-wireframe**, add an ellipse (O) measuring **55×55** with a default drop shadow and fill.

Place this button on the bottom-right of the frame, above the **bottom-navigation** layer. Give it a margin of **32** from the right and bottom and name the layer **filter-button**.

Instead of taking the user to a different screen, it’s more convenient to display the filter as a dismissable option in the list screen. The user can filter the list based on genre, release year, duration and director.

Duplicate the **movie-list-wireframe** frame and name it **movie-list-wireframe- filter**. Delete the **filter-button** from this frame.

Lock the movies-list and bottom-navigation layers since you aren’t going to make any changes to them.

Now, add a rectangle (R) to this frame measuring **414×896** above both layers. Reduce the opacity of this layer to **70%**, name it **filter-background** and lock the layer.

Here’s how your layer arrangement should look at this point:

Add a rectangle measuring **414×448** and give it a white fill. Constrain this layer to the **Center** and **Bottom** and name it **filter-sheet**. This sheet will house the options to filter the list.

Add a text layer (T) on the filter-sheet layer with the text **Genre**. Use **Roboto- Regular** as the font with size **18**. Place it at a margin of **16** from the **top** and **left** and name the layer **filter-header**.

Add a **genres** component and place it below the filter-header at a margin of **16** from the top and left.

Duplicate the modified **genres** component three times to get a total of 12 genres, as shown below.

Repeat the process to create the release year, duration and director filter categories. Here’s how it should look when you’re done.

You did a great job building a good chunk of the filter UI on your own. If you’re unsure about the margins and positioning of the different filter categories and their options, you can refer to the final project file, **chapter-3-final.fig**, and see how they look in the finished project.

The list screen now offers the ability to filter the movie per the user’s preferences.

Ideally, when wireframing a feature or a screen, you’d build at least three to four alternatives and compare them against each other. This helps you uncover areas that need further polish.

3-4个方案对比

But for now, you’ll move on to working on the movie details UI.

#### **Designing the Movie Detail Screen**

The detail screen involves a few more bits than the list screen does. This screen will show:

- All the information from the movie-list-item
- The movie synopsis
- Cast details
- Movie ratings
- An “Add to watchlist” option

You’ll reuse most of the information from the movie-list-item on this screen. Add an **iPhone 11 Pro Max** frame (F) to the canvas. Name the frame **movie-details- wireframe**

Add a **movie-image** component at a margin of **40** from the top, and change the dimension to **174×262**. Align it horizontally to the frame.

Copy over the **movie-name** and **movie info** text layers from one of the previous frames and change the movie-name text alignment to **Text-Align Center**.

Place the movie-name below the movie-image with a margin of **40** from the top. Be sure to keep everything horizontally centered in the frame.

Place the movie-info layer below the movie name, horizontally centered, at a margin of **32** from the top. Now, add the **genres** and **rating** components to the frame. Place the genres below movie-info and the rating below the genres. Give both a margin of **24**. You’ll notice they are quite small for the screen.

Select the genres and press **K** to enter **Scale** mode. Alternatively, you can select **Scale** from the toolbar at the top of the screen.

Drag the corner handles of the genre row and increase the dimensions to **218×23** approximately.

Now, select the rating row and scale the dimension to **237x36**. Make sure all layers added to the frame so far are horizontally aligned.

Next, add a text layer (T) for the synopsis. Don’t worry about displaying an actual synopsis here. You can now use placeholder text to get a feel of how things will look with real data in place. A great website to pick up some placeholder text is http:// lipsum.com.

Paste nine lines of text onto the text layer. Name this layer **movie-synopsis**.

Center the text and horizontally align the layer to the frame. Use **Roboto-Regular** as the font, with a font size of **12**. Position the synopsis at a margin of **32** from the rating row.

For the cast section, add a text layer (T) for the section header with **Cast**. Name this layer **section-header**.

Use **Roboto-Regular** font with a font size of **24** and place it at a margin of **64** below the synopsis layer and **16** from the left.

At this point, you might have run out of room to add more layers to the frame. Select the frame name from the Layers panel and either drag its handles vertically to increase the height or manually increase the frame’s height to **1587**. This should give you enough room to add the remaining sections to the screen.

Now, add a movie-image component and change the dimension to be **63×63**. Place it below the section header layer at a margin of **24** from the top and **16** from the left. Name this layer **cast-image**.

Add a text layer (T) for the cast name below the cast-image with a font size of **16** and align it horizontally with the cast-image. Use **John Doe** as the text. Name this layer **cast-name**. Group cast-image and cast-name and call the group **cast-member**. Duplicate cast-member five times and, with all five selected, use Auto Layout (Shift- A) with a horizontal spacing of **24**.

Now, change the name of each cast-member and name this auto layout **cast-list**.

Moving along, duplicate the **section-header** you created earlier. Change the text to **Ratings** and place it below cast-list with a margin of **64**.

To create the rating list, duplicate the **cast-name** and **cast-image** you created earlier. Place the cast-image below the section-header with a margin of **24** from the top and **16** from the left. Vertically align the cast-name to the image and place it at a margin of **24** from the left.

Add a rating component and place it below **cast-name** at a margin of **8** from the top and **24** from the left. Group the cast-name, cast-image and rating and name the group **review-list-item**.

Next, duplicate review-list-item four times and, with all four selected, use Auto Layout (Shift-A). Select **Vertical** from the auto layout properties and set **Spacing Between Items** to **32**.

#### **Implementing the “Add to Watchlist” Feature**

The last missing piece on this screen is an option to add a movie to a watch list. To do this, add a **382×58** rectangle (R) to the screen. Give it a **black** stroke and a fill of **#C4C4C4**.

Now, add the text **Add to Watchlist** on a text layer (T) with a font of **Roboto- Medium** and a font size of **24**. Center the text on the rectangle and group the elements.

Name the group **favorite-button** and place it at the bottom of the screen with a margin of **16** from the sides and bottom.

The cast and rating sections are distinguishable at this point, but there’s no grouping of the actual movie information. You’ll add a backdrop to fix this.

#### **Grouping the Movie Information**

Start by adding a rectangle (R) measuring **379×590** with a corner radius of **8**, a **black** stroke and a fill of **#F5F1F1**. Call this rectangle **backdrop** and make it the last layer on the Layers panel for this frame. Next, horizontally align it to the frame and position it at a margin of **171** from the top.

#### **Marking Movies as Seen**

The last thing to do on this screen is to add the ability to mark a movie in the user’s watchlist, as seen. This only requires you to add an extra state to the button.

Duplicate the **movie-details-wireframe** and change the button text to **Mark as Seen**. Once done, you should have two states of the detail screen, as shown below.

The detail screen looks much better now. The sections of the screen are clearly separated, which lets users quickly scan the information, and it offers controls to curate the user’s watch list.

Great job with your first wireframe! You just completed one of the essential steps towards building a great app. By going through the wireframing process, you built out the core of what the app will feel like. Going forward, you have a strong foundation and a clear idea of which use cases you’re catering to.

Ideally, wireframing is the stage where developers and designers come together to talk out any engineering constraints that might come up during the final implementation. This gives the designers a clear picture of the boundaries and limitations they should account for.

Try getting involved in the wireframing process next time you work with your designers. They’ll appreciate the early feedback and input, as it’s much easier to iterate and incorporate changes at this stage, when the designs are still rudimentary.

In the next chapter, you’ll build on top of these wireframes and add real data to bring the screens to life.