### Colors

In the last chapter, you focused on learning the fundamentals of one of the more subtle yet influential design aspects, typography. You started with a quick primer on typography fundamentals, then built a typographic scale from scratch.

In this chapter, you’ll focus on another essential element: colors.

When you use them correctly and tastefully, colors help users and provide visual feedback for eventful interactions, like highlighting errors and providing confirmation.

Colors are crucial for establishing brand identity and helping a product stand out. They bring uniformity and cohesion to a family of related products. While they’re a subtle aspect of design, colors make a big impact on how users perceive and remember your brand.

Great examples of products that leverage color to establish their identity are Coca- Cola’s brilliant red, McLaren’s papaya orange and Apple’s use of white across their products and packaging. Often underrated, colors build an instant association with a brand identity.

Before getting into building a palette, you’ll walk through some fundamental concepts of color theory to help you pick the right colors for your project.

#### **Basics of Color Theory**

Simply put, color theory is a collection of guidelines that designers use to pick colors. These guidelines are based upon how humans perceive colors, which emotions and messages they convey and what visual effects you can achieve when you mix them.

Now, you’ll start from the ground up by understanding what a color is.

Color is a perception. When our eyes see an object, they send signals to our brains, which our brains use to define the color.

These signals are wavelengths of light reflected by objects.

#### **Color Wheel**

The first color wheel was designed by Sir Isaac Newton in the 1600s. Designers still use it today to mix and match colors to build a palette.

A color wheel contains: 

**Three primary colors**:

- Red
- Yellow
- Blue

**Three secondary colors**, derived from mixing the primary colors:

- Blue plus yellow = green
- Red plus yellow = orange
- Blue plus red = purple

**Six tertiary colors**, derived from mixing the primary and secondary colors:

- Red-orange
- Red-purple
- Blue-purple
- Blue-green
- Yellow-green
- Yellow-orange

#### **Hue**

Hue is the name of the color: red, blue, green, yellow, orange and so on.

#### **Saturation**

Saturation is the intensity or purity of a hue. High saturation makes a color look bright and vibrant, whereas a desaturated color is more washed out and subtle.

#### **Value**

Value determines the degree of darkness or lightness of a hue, ranging from pure white to pure black.

#### **Shade**

A shade is a hue produced by adding black in varying amounts. The image below shows various shades of red with increasing amounts of black added to it.

#### **Tint**

A tint is a hue produced by adding white in varying amounts. The image below shows various tints of red with increasing amounts of white added to it.

#### **Color Temperature**

Divide the color wheel vertically into two halves. The colors on the left half are the **warm** colors — reds, oranges and yellows. The ones on the right half are the **cool** colors — purples, blues and greens.

#### **Contrast**

In color theory, contrast is the difference in the visual properties of an object that distinguishes it from other objects and its own background. In other words, it’s the difference between two colors.

In web and mobile design, using contrasting colors is essential to ensuring accessibility and readability. You must always strive to create designs that have a strong contrast between the content and background to ensure users with visual impairments aren’t left out.

The **Web Content Accessibility Guidelines (WCAG) 2** recommends a minimum contrast ratio of

- 4.5:1 for regular text
- 3:1 for large text, graphics and user interface components

There are several online tools available to check the contrast ratios of your colors, like WebAIM’s contrast checker: webaim.org/resources/contrastchecker/.

You can feed the background and foreground colors into this tool and get the resulting ratio. These ratios are measured using a grading system called **levels of conformance**. The highest achievable grade on this system is **AAA**, which requires a **7:1** ratio.

In the image below, you can see an example of a poor contrast ratio:

In comparison, here’s what a good contrast ratio looks like:

Sometimes, you can’t — or shouldn’t try to — achieve the **AAA** grade, as it requires highly complementary colors, which might make your palette dull or unappealing.

Now that you have a basic understanding of colors and contrast, it’s time to understand how to pick colors using tried and tested color schemes.

#### **Color Schemes**

Color schemes are formulas based on color harmony. In color theory, color harmonies are sets of colors that look good together.

#### **Monochromatic Scheme**

The simplest form of a color scheme is the monochromatic color scheme. You start with a single color on the color wheel, then create variations using the knowledge of saturation and values.

#### **Analogous Scheme**

An analogous color scheme uses colors next to each other on the color wheel, like reds and oranges or greens and blues.

#### **Complementary Scheme**

A complementary color scheme uses colors opposite one other on the color wheel, like blue and orange or red and green.

#### **Split Complementary Scheme**

A split complementary color scheme uses colors on either side of the complement of the base color. This scheme provides the same contrast level as the complementary scheme, but gives you more colors to work with.

#### **Triadic Scheme**

A triadic color scheme uses three colors that are evenly spaced on the color wheel, forming a perfect triangle.

#### **Tetradic Scheme**

A tetradic color scheme uses four colors that form a rectangle on the color wheel, and the diagonals are complementary pairs.

It’s worth noting that color schemes are guidelines, or a starting point to build your palette. They aren’t hard rules you must follow. Depending on your use case, your brand needs and your contrast constraints, feel free to tweak properties as and when you need to.

Whichever color scheme you choose, it’s important that the palette you choose doesn’t hamper the legibility and readability of text on your product.

#### **Finding the Right Scheme for Your Project**

While colors are an important part of design, they’re by no means a must-have requirement for every project, whether print or digital. An informational website, like a blog or an online journal, can deliver a great experience while sticking to a monochromatic palette that uses black text on a white background.

The app you’re working on doesn’t use colors heavily because it relies on the movie posters for vibrancy. That said, the app still uses colors for visual feedback, like the states of the bottom navigation buttons, the ratings component and the **Add to Watchlist** button.

Generating and experimenting with different palettes is outside the scope of this chapter, as you often need to account for the brand guidelines of the company. Still, if you want to generate and test different color combinations for your projects, check out the following:

- **Adobe Colors**: https://color.adobe.com
- **Material Color Tool**: https://material.io/resources/color

With these resources, you can use what you’ve learned here to quickly build and experiment with different color styles.

You’ve only scratched the surface of color theory here, but you’ve learned enough to get started.

#### **Building Your Palette**

Compared to building the type scale, your palette is much more straightforward because you’ll just convert the colors you’re already using into reusable styles. This saves you the trouble of manually copy-pasting the hex codes going forward.

At this point, you might wonder what the idea behind selecting Cinematic’s colors was.

The movie posters in the app are already vibrant and loud enough to draw the user’s attention. Any additional colors you use for UI elements, like buttons and ratings, must be bold and vibrant enough to make it easy for users to discover these interactions and not feel lost.

That was the primary motivation behind picking the current palette. The palette uses the triadic color scheme with the saturation of each color cranked up to eleven!

Now that you know the reasoning behind selecting the palette, it’s time to build the color styles.

Download this chapter’s materials, then open **chapter-7-starter.fig** in Figma. This file picks up where the last chapter left off.

#### **Turning Your Colors Into Reusable Styles**

Now, it’s time to finalize the color styles for this app. Create a new page and name it **Colors**.

Add a new **400×300** frame (**F**) to the canvas.

Name this frame **Palette**. Add a **30×30** ellipse (**O**) to the frame and give it a fill of **#F34444**.

Creating a color style is relatively straightforward. In the **Fill** section, click on the **Style** icon, which looks like four circles.

In the menu that pops up, click the **+** icon and name this style **button/primary**.

The **category/subcategory** notation lets you create categories within the styles.

Add another **30×30** ellipse (**O**) and give it a fill of **#6D6E70**. Then create a new style

and name it **navigation/inactive**.

The active icon in the navigation has a deep blue color, so create a new **30×30** ellipse (**O**) for it and give it a fill of **#0045D0**. Finally, create a new style named **navigation/ active**.

Why aren’t you focusing on the circles’ position and alignment? That’s because, once you create a color style, you can delete the circle and the style is still available for you to use.

You want to quickly build a palette so you can apply the styles to your designs. Nevertheless, you should keep a rudimentary arrangement in place so it’s easy to differentiate between different styles.

#### **Creating the Text Color Styles**

Keep moving along to create the text color styles. Add another **30×30** ellipse (**O**) and give it a **#000000** fill and create a style named **text/primary**.

Create another **30×30** ellipse (**O**) with a **#ffffff** fill. At this point, the white circle isn’t visible because the frame you’re working on is also white.

To create contrast between the frame and the individual color styles, change the frame’s fill color to **#D2D2D2**. This will differentiate between the styles and the frame.

Create a new color style from the circle with white fill and name it **text/secondary**.

Create another **30×30** ellipse (**O**) with a fill of **#B9C95A** and name this color **text/ accent**.

Finally, create still another **30×30** ellipse (**O**) with a fill of **#727272** and name this color **text/muted**.

#### **Using Gradients in Color Styles**

Color styles aren’t limited to solid fills; they can also hold gradients. The movie posters in the app use a gradient scrim to make the text more visible. Your next step is to make this gradient a style, so if you need to change things later, you only need to do it in a single place.

Add a **30×30** ellipse (**O**) and, instead of giving it a solid color fill, use a **Linear** gradient.

The left handle will have a **#000000** fill with **8%** opacity, while the right handle will have a **#000000** fill with **100%** opacity.

Create a new style using this circle and name it **gradient/poster**.

#### **Styling the Rating Component**

For your final step in defining your palette, you’ll create styles for your rating component.

Add a **30×30** ellipse (**O**) with a fill of **#89E045** and name it **rating/positive**.

Create another ellipse (**O**) and give it a fill of **#C4C4C4**, which also happens to be the default fill color in Figma. Name this style **rating/neutral**.

And that’s it! Great job building your color style catalog. If you deselect everything on the canvas, you’ll see all your color styles appear on the right.

The naming schematic you used will be helpful when assigning styles to your components.

#### **Applying the Color Styles**

Head to your **Components** page to apply the color styles you just created. First up: the posters. Select the **backdrop-gradient** layer from the **Poster/Birds of Prey** component.

Now, click the **Fill** section’s **Style** button.

Select the **gradient/poster** style to remove the existing fill and apply the color style.

Repeat this process for the remaining posters.

Next, the movie cards. Select the **title** layer and change the fill to **text/secondary**.

Repeat the same process for the three text labels inside the **movie-info** group.

In a moment, you’ll tackle the genre and ratings in their component frame. For now, repeat the process and change the fill of the text layers inside the **movie card/ rounded** component.

Then, change the fill style for the **genres** text in the **genres** component.

You also need to change the fill for the enclosing Auto Layout frames.

#### **Styling the Icons**

Before tackling the navigation component, take a moment to work with the icons. Select the individual icon paths in the **Icons** frame.

Give them a fill style of **navigation/inactive**.

Now, in the **bottom-nav** component, select the **trending** icon and its text label and change their fill to **navigation/active**.

Repeat the same process for the text labels for each navigation target.

For the final piece of the **Components** page, change the **rating** component text’s fill to **text/secondary**.

Finally, apply the relevant **rating/positive** fill to the stars.

#### **Updating the Details Screen**

Great job with the styles so far. Since the app uses components in most places, Figma will make a good chunk of the changes automatically. You only need to change the app’s details screen.

Open the **Cinematic App** page and, in the **movie-details** frame, change:

- The **title text** color to **text/primary**.
- The **movie-info text** color to **text/muted**.
- All **section header text** colors to **text/primary**.
- The **synopsis text** color to **text/primary**.
- All **user rating text** color to **text/muted**.
- The **button text** color to **text/secondary**.
- The **button fill** to **button/primary**.

After all the modifications are in place, your screens will finally look like this:

#### **Advantages of Color Styles**

After all that work, none of the visuals in the app have changed. However, you’ve introduced flexibility that allows you to change the palette and, in turn, the app’s styling — without impacting the rest of the design.

This is a great time to use an analogy from engineering. Engineers often practice separation of concerns to reduce tight coupling within their projects. By isolating functionality into separate layers, you reduce the scope of change when you make modifications. This also ensures you don’t unknowingly break existing functionality.

You reap those benefits by keeping your styling — i.e., typography and colors — decoupled from your design. When you broke designs into components in the previous chapter, you saw how straightforward it is to switch things around when you needed to. By creating text and color styles, you can make more granular changes to those components with the same advantages.

Congratulations, you’ve done a great job with the design so far. The screens look appealing and, more importantly, you’ve laid the groundwork to make future modifications less time-consuming.

In the next chapter, you’ll bring these screens to life by adding animations and transitions.

#### **Key Points**

- You learned the basics of color theory and its importance in design.
- Then you learned about the importance of contrast in design for accessibility.
- You created color styles for the UI elements of the app.
- You then applied the color styles to the components.
- Finally, you learned about the benefits of using reusable styles.