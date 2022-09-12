### CSS Transitons and Animations

CSS transforms, CSS transitions, and CSS animations are three separate CSS specifications. CSS transitions and animations make things move *over time*. Transitions and animations let you define the transition between two or more states of an element.

Transforms change an element’s appearance through translation, rotation, scaling, and skewing, but do not have a time component. You can use the CSS transform property to change the location of an element’s coordinate space of the CSS visual for‐ matting model, but you need transitions or animation to make that change occur over time.

Transform定义的是translation，rotation，scaling和skewing，而没有时间元素

While animation is possible with JavaScript, understanding CSS3 transitions and ani‐ mations will save you a lot of time and effort if you need to animate anything on the Web. Generally, it will also save your users’ CPU and battery compared to JavaScript.

CSS3的animation节省CPU和耗能

Used correctly, CSS animations and transitions can give your web applications life and depth. While this booklet focuses on *how* to transition and animate elements in your documents, understanding *when* to use animation can improve your user expe‐ rience (UX) as well. By adding the dimension of time, animating can help your UX communicate on a different level.

#### 12 Basic Principles of Animation

Well before the advent of the Web, animators at Disney came up with 12 principles for cartoon animation. Some of these principles are very relevant to CSS animation as well.

According to the “bible of animation”—*The* *Illusion of Life: Disney Animation* by Frank Thomas and Ollie Johnston—there are 12 basic principles for animation, including:

这里借用Disney提出的卡通漫画的12原则

1. Squash and stretch

Depending on what something is made of, objects deform under motion. *Squashing and stretching* gives the illusion of weight and volume to an object or character as it bounces or otherwise moves. For example, when a ball bounces, it’s squashed as it hits the ground and stretches as it heads upward.

挤压和延伸；比如球的bounce，当球触地则挤压，弹起则延伸

2. Anticipation

Users may not understand an animation unless there is a sequence of actions that leads clearly from one activity to the next. They must *anticipate* or expect a change before it actually occurs. In cartoons, it’s a movement preparing the viewer for a major action the character is about to perform, like a knee bending before a jump. On the Web, it could be a button depression before the start of the more extensive animation that starts when the button is selected.

Guide your users mentally to where they should focus before initiating the main effect, especially if the start of the animation is important.

需要给用户一个运动和效果的预期。

3. Staging

In a cartoon, *staging* is the presentation of an idea so it is unmistakably clear. On the Web, staging is directing the user’s attention to an action, such as a small jiggle of the call-to-action button. Staging helps guide users through the story or idea being told: for example, through the steps of a check-out process.

需要让用户要连续的集中注意力

4. Straight ahead action and pose to pose

There are two main approaches to animation on the big screen. In the *straight ahead* action approach, the animator starts at the first drawing and works drawing to drawing to the end of a scene. In *pose to pose*, the animator draws the main points within an animation and creates (or has an assistant create) the points in between later. While seemingly only applicable to storyboarding, this principle is also related to drawing keyframes and how the animation fills in the space or time between them.

With CSS animations, when we animate image sprites to create motion, we are emulating the Straight Ahead approach. This is described when we cover the steps values of the animation-timing-function property. In most animation scenarios, we let the browser be our assistant, defining specific points, or poses, within the animation and allowing the browser to interpolate property values as it animates from pose to pose, or keyframe definition to keyframe definition.

Straight ahead: 从开始一画一画到最后渲染

pose to pose: 只画出关键点，中间由其他部分补充

5. Follow-through adn overlapping action

*Follow-through* is the inclusion of additional motion when the main animation concludes. For example, if a character is running and stops, her hair and clothes were likely bouncing and fall back into place after her legs and body cease moving, catching up to the main mass of the character. Nothing stops all at once.

*Overlapping action* is when some components are slightly delayed after other components change direction, like the way Wile E. Coyote’s legs keep moving forward as he drops off a cliff. His ears take a moment to follow.

If your CSS animations ever become complex enough to require follow-through and overlapping action, timing will be critical to making your effects work.

Follow-through: 在主动画结束时包含其他运动

Overlapping action: 当某些组件在其他组件改变方向后其他组件略微延迟时

6. Slow in and slow out

Just like cars don’t start and end at full speed—rather, they accelerate from stopped to full speed and decelerate back to zero—slow ins and slow outs make animation more lifelike and soften the action. Only mechanical animations will proceed at a linear speed.

The *slow in and slow out* principle states that the beginning and end of an animation are more interesting than its middle; and therefore, unless it’s mechanical, the animation should proceed fastest in the middle of the animation, with a slower start and slower end. With cartoon animation, the effect is created by having more cells at the ends and fewer in the middle of the action. With CSS, this effect is created by setting cubic Bézier timing functions to something other than linear.

slow-in and slow-out：模拟动作在开始和结束等节点的速度变化

7. Arcs

The *arcs* principle states that almost all actions follow an arc or slightly circular path. Think of your hand moving back and forth as you walk: your hand arcs back and forth rather than always staying at an equal distance from the ground.

Linear animations can be very mechanical. Arcs can be achieved with granular control within a keyframe animation. Because of this, CSS developers often use CSS animations instead of transitions; animations offer more granularity in cre‐ ating an arced path, while CSS transitions only allow for moving between two states. However, with some cubic Bézier timing functions, creating an arc with CSS transitions is actually not only possible, but fairly simple.

几乎所有的动作都遵循一个arc或slightly circular path。

8. *Secondary action*

*Secondary actions* can enrich a main action by adding dimension, supplementing or reinforcing the main action and giving the scene more life. If you include sec‐ ondary actions, the animations should work together in support of one another.

For example, if your main animation drops a module onto a page, a secondary action might be the main call-to-action button within the module dropping into place, then finishing its action a little after the main module has finished animat‐ ing in. A secondary action should reinforce the main action. It is OK to literally “think outside the box” and animate a child element differently from its parent.

Secondary action通过添加dimension，supolementing或者reinforcing主动作而给主动作更多的真实感

9. Timing

*Timing* is likely the most important of the principles in this list when it comes to animation. While in traditional animation it’s based on the number of frames, in CSS animation it has more to do with creating the appropriate amount of time to read the motion, but not so long as to make the site appear slow. Timing includes not just the duration of the animation, but also the delay and timing function.

There are no right answers when it comes to timing. Expertise in timing comes with experience and experimentation, if it comes at all. I recommend using trial and error to refine the timing of your animation, then cut your times in half: while you may want your animation to progress slowly enough to grok the difference in times and discover what is the best combination of duration, delay, and progression, you don’t want your site to appear slow.

时间是最重要的原则。时间不仅仅包含动画的duration而且包含delay和时间函数

10. Exaggration

*Exaggeration* is the highlighting of movements beyond their natural state to call attention to what you want the user to focus on. A tiny bit of exaggeration can give added life to an animation and actually make it look more realistic. Use good taste and common sense: exaggeration is not extreme distortion, but rather a slight distortion that gives emphasis without being so exaggerated as to be visibly distorted for your visitors.

夸大可以call attention to希望用户focus on的点

11. *Solid drawing*

The *solid drawing* principle includes the principles of drawing or coding forms that convey the illusion of three dimensions, with weight and solid form. In CSS, this includes using box shadows, gradients, and transforms, giving your content the illusion of being three-dimensional. If you aren’t doing 3D animation or spriting, this principle is only tangentially related to web animation, as on the Web, we’re drawing with CSS in a two-dimensional space.

Solid drawing包含three dimensions的illusion。在CSS中，这包含使用box shadows, gradients和transforms。非3D则关系不大

12. *Appeal*

The *appeal* principle has to do with charisma, believability, and interest. Appeal on the Web includes an easy-to-read design, clear drawing, and motion that will capture and involve the visitor’s interest. The animation has to appeal to the mind as well as to the eye.

吸引力原则

#### Animation and Transition Considerations

Flash animation, animated banner ads, and Geocities animated GIFs from 1996 have given animation a bad name. In reality, it isn’t that animation itself is bad; it’s the way those animations were implemented that led to bad user experiences. With CSS, we can create animations and transitions that improve and augment user experience. Animation can give the appearance of life to two-dimensional, lifeless objects and give understanding to objects that have no meaning. But with great power comes great responsibility.

Animations can be used for good: a small animation can help inform the user that the state of an object has changed, or can occupy the user’s attention so they don’t notice a slow loading time.

The most important principle is timing and it’s the most difficult to fine-tune. Unlike cartoons, timing in CSS means milliseconds, not frames. There is no magic value of milliseconds for the duration. Depending on the timing function, you may want to add a few milliseconds for the effect to feel natural. A developer-defined cubic Bézier timing function that creates a bounce might work better with a bit more time than a simple ease-in timing function. Just ensure the animation is short enough for your site to feel responsive rather than slow.

If you have many animations in your site or application, think of timing as a choreography of animations. There is no “right” number of animations. It’s not the number of animations, it’s the design behind it.

All your animations should feel related. If there is a mix and match of delays, timings, and timing functions, the animations may seem unrelated. Choreograph your animations to ensure a cohesive feel among all the moving parts. Make sure your animations are making your site more sophisticated, more modern, and more trustworthy.

It is up to your designers to design animations that have purpose and style. It’s up to this book to teach you how to implement those animations.

要注意animation的编排