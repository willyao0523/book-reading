### Typography

In the previous chapter, you created reusable components that you could use to build your screens. By the end of the chapter, you had a fully functional main screen with a movie list and a details screen with controls to add a movie to a watchlist.

Now, it’s time to focus on the more subtle aspects of design: typography and color.

字体和颜色

Typography and color are both essential elements of design. It might seem like they’re only cosmetic, but they have a broad impact. They not only help you establish brand cohesion and identity, but they also determine how inclusive and accessible your product is.

Though closely related, they deserve individual chapters to drive home the idea and get the basics right.

This chapter will focus on the details of typography and how to build a typographic scale. Chapter 7, “Colors” will go over color theory and how to build a color palette.

These two chapters are my favorite in the book because I get to nerd out about minor details and show you how subtle changes can have a massive influence on the perception and feel of a product. It’s one of those things that are rather tricky to get right, but very easy to get wrong.

很容易犯错

By the end of this chapter, you’ll realize what makes your style choices “good” and how you can leverage typography to add an element of personality to your designs.

While both iOS and Android have established and mature design guidelines for building apps, you won’t use them for the typographic scale here. Deriving your design from the guidelines at this stage would be restrictive and wouldn’t give you a feel for the process from start to finish.

By building your typographic scale from scratch, you’ll get first-hand experience of why things are done the way they are, helping you understand the reasoning behind certain decisions.

#### **Understanding Typography**

In simple words, typography is the art of arranging letters in a legible, clear way that conveys intent to the reader. Typography affects visual appeal and invokes specific emotions in the reader, leaving a lasting perception about the app.

字体就是用简洁的方式组合字母传递意念

Emotion? Perception? If you wonder how these are relevant to typography, here’s an example: You need to get your wisdom teeth pulled, so you look for dentists in your area on Yelp. You come across this sign:

This looks familiar and calm, inspiring confidence. If you choose this dentist, you’ll probably have a normal procedure and be out in no time.

Now, imagine the sign looks like this, instead:

You’d probably run in the opposite direction from this clinic!

Typography is versatile enough to convey:

When done right, a good typographic scale helps establish hierarchy and uniformity across products. It helps your users immediately recognize your brand.

Before you dive into creating your typographic scale for the app, take a moment to learn some important terminology to help you better understand the explanations that follow.

#### **Mastering Typographical Jargon**

Fonts and typefaces are often confused in typography.

Typefaces are a collection of different type styles that vary in character size and weight. A font is a part of a typeface — that is, it represents one of the variants. In other words, a typeface is a collection of many fonts. Here’s an example:

Typeface是不同单字大小和weight的不同type风格的集合。font是一部分的typeface。typeface是fonts的集合

Typefaces are roughly classified into four categories:

- Serif
- Sans-serif(without self)
- Script
- Decorative

Serif and sans-serif typefaces the most widely used so it’s essential to recognize the difference between them. Serif typefaces have a distinguished, extended stem — or feet — in their letters. Their sans-serif counterparts do not.

Some popular serif typefaces are: Times New Roman, Garamond, Baskerville and Georgia. Popular sans-serif typefaces include: Helvetica, Arial, Roboto and Open Sans.

Serif字体字脚变大

#### **Weight**

A typeface usually comes in three to four weights. Weight is the relative thickness of a font’s stroke. You’ve been using weights all this time, and now you know what they mean.

weight：字体厚度

For example, the Roboto font comes in six weights: thin, light, regular, medium, bold and black.

#### **Letter Spacing**

Letter spacing, also referred to as tracking, is the space between individual letters. Upper-case letters have loose tracking, while lower-case letters have tighter tracking.

Design tools like Figma let you modify the letter spacing. This is useful in some instances, such as when you want to create alternate variants in a typographic scale.

#### **Baseline**

The baseline is an invisible line that the text sits on, as shown below. Think of it as the lines in a notebook that help you write evenly.

Baselines are important to determine an essential aspect of text blocks: line-height.

基线对决定line-height很重要

#### **Line-height**

Line-height is the distance between the baseline of two consecutive lines of text. Line-height is crucial for the readability of text. If you use a tight line-height, your lines will hug one another. If the line-height is loose, it will be difficult to break your text into chunks or paragraphs.

连续两行基线的间距

#### **Paragraph Spacing**

As the name suggests, paragraph spacing is the space between two separate blocks of text. Paragraph spacing is influenced by line-height. It’s easy to confuse loose line- height with paragraph spacing, which disrupts continuity when reading.

There are many other typographical terms that you won’t need for this chapter. If you’re curious and want to learn more, a great place to start is with the **Understanding Typography** sub-section of the **Material Design** typography guidelines. **paragraphSpacing**: https://material.io/design/typography/ understanding-typography.html

Now that you know some critical terminology, it’s time to get to work.

#### **Building a Typographic Scale**

A typographic scale is almost like a musical scale. It uses fonts that work harmoniously with each other and complement each other’s attributes.

Just as harmonics do, text styles in a typographic scale grow and shrink in a certain pattern to produce a set of styles that work well with one other.

The first step in building a typographic scale is to determine a **base text style** to start from. This text style represents the styling of the body text. Think of the body text as the text that goes inside a paragraph tag on the web.

从body字体开始，用于paragraph

Once you have the base text style, you scale up or down, depending on your use case, using a **scale factor** to produce variations.

通过scale factor把其他的字体拿到

A scale factor is a numeric value. You multiply your base style values by the scale factor to obtain the upscaled and downscaled variants.

You don’t have to strictly abide by the scale factor values; you can tweak the scaled variants depending on your use case. The objective is to produce a scale that doesn’t compromise legibility.

不影响易读性的scale

#### **Getting Started**

Download this chapter’s materials, then open **chapter-6-starter.fig** in Figma. This file picks up where the last chapter left off.

Create a new page in this file and name it **Typography**.

Next, add a MacBook frame (**F**) with a width and height of **500** to the canvas and name it **Text Styles**.

#### **Defining the Base Text Style**

You’ll start with a base text style with a font size of **14**. Add a text layer (**T**) to the frame with the text **Text Style — 14**. Use **Roboto** as the font with a **Regular** weight and font size of **14**.

You’ll choose font sizes for the various elements of your app based on a modular scale, which is popular in web design. Simply put, it’s a specific sequence of numbers that relate to one another in a meaningful way. Using a modular scale as a base for a typographic scale makes your text styles scale up and down proportionally.

In this case, you’ll use a scale factor of **1.3**, which is a simplified derivative of the perfect fourths scale. You’ll start with a weight and then multiply or divide by 1.3 to get larger or smaller sizes.

Here, when you apply the 1.3 scale factor to the base text style, you’ll get the following size variants:

##### **Evening Your Font Sizes**

After that multiplication, you end up with types in fractional font sizes — which aren’t convenient to work with. Implementing fractional types in code is messy. It’s usually best to use type values that are even.

最好使用偶数的type values

So far, the chapters have all used margins and alignment metrics in multiples of eight, and there’s a specific reason why: When aligning and spacing items using increments of eight, your designs scale appropriately on all screen sizes. Most screen sizes today are divisible by eight, which makes aligning things easy.

现在的很多屏幕大小可以被8整除

Naturally, if your type scales use even values, you aren’t working with half-pixel values. This ensures a continuous rhythm in your designs, making it easy to manage and to scale things as needed.

偶数值不好处理0.5

This is also a great time to point out that building a type system is not cut and dry; designers often need to tweak values after starting from a base scale. That means you have the freedom to deviate from the scales when it makes sense. Remember, the scale factor gives you a blueprint to build your typographic scale off of, but you should think of the scale as a flexible guideline instead of a hard rule.

不能把scale factor当作教条

With these facts in mind, you’ll tweak your values to play well with your designs by rounding the values to the nearest even number. So 18.2 becomes 18, 23.66 becomes 24, and 30.76 becomes 30.

For your next step, duplicate the text layer you created earlier three times vertically and change the text and font size, as shown below. The numeric value for each style represents its font size.

#### **Text Styles**

Now that you have a basic scale in place, you’ll incorporate it into your use case. 

In the app, you need text styles for:

- Title Text
- Movie Info
- Genres
- Rating value
- Details title text
- Synopsis
- Details section header
- User ratings
- Button text

Based on these requirements, your next step is to create the typographic scale from the base text style. You’ll work from the top down, with the largest text size first and the smallest ones coming last.

#### **Defining Text Styles for Your Elements**

The largest text in the entire app is the movie rating, so you’ll use the largest text in your scale for that element. To do that, duplicate the **Text Style — 30** layer and change the text to **Rating**.

For the font, use **Rubik One**.

The movie title comes next. Duplicate the **Text Style — 24** layer and place it below the **Rating** layer at a margin of **8** from the top. Change the text to **Title** and the font to **Chivo** with a weight of **Bold**.

For the details screen’s section headers, duplicate **Text Style — 18** and place it below the **Title** text. Change the text to **Section Header** and change the font to **Chivo** with a weight of **Bold**.

For the button text, duplicate **Text Style — 18** again and place it below the **Section Header** text. Change the text to **Button Text** and the font to **Rubik** with a weight of **Medium**.

Great progress, keep moving along!

Duplicate the **Text Style — 14** layer and place it below the **Button Text** layer. Change the text to **Movie Info** and the font to **Chivo** with a weight of **Regular**.

Repeat the same step by duplicating the **Text Style — 14** layer again, changing the text to **Body Text**, and the font to **Chivo** with a weight of **Regular**. Place this layer below the **Movie Info** layer.

Once again, duplicate **Text Style — 14** and change the text to **Navigation Text**. Use **Rubik** with a weight of **Regular** as the font. Place this layer below the **Body Text** layer.

Finally, repeat the same step by duplicating **Text Style — 14** and changing the text to **User Rating Text** and the font to **Rubik** with a weight of **Regular**. Place this layer below the **Navigation Text** layer.

You might wonder why you created the same text style twice for the **Movie Info** and the **Body Text**, then again for the **Navigation Text** and **User Rating**.

First of all, that’s a great question and a sign you’re noticing the finer details.

You’re correct, you could have reused the **Movie Info** style for the body text and the **Navigation Text** style for the user rating text. But having separate text styles for separate use cases, even though their styling is the same, allows you to flexibly change the attributes of one element in the future without affecting other elements. This means you can switch the font of the synopsis text on the detail page without having to worry about messing up the movie info text style.

不用担心一处影响了不相干的部分

At this point, the only text element you don’t have a style for is the genre text. You could reuse the **Text Style - 14** for this, but the typographic scale would then lack hierarchy. Fonts, weights, and font sizes work together to break the screen into chunks that are easy for your users’ eyes to scan.