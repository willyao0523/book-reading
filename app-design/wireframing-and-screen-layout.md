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