---
title: "TIL: React Aria - useButton"
date: 2021-04-26
tags:
    - TIL
    - React
    - A11y
layout: post.njk
---

I had a lot of fun today playing with React Aria - a library of React Hooks that provides accessible UI primitives for your design system.

Though there library is pretty big I only touched <a href="https://react-spectrum.adobe.com/react-aria/useButton.html">useButton</a> a bit. But so far I'm really impressed and consider giving this tool a shot.

On example of a Button component:

<strong>Before</strong>

* necessity to support onClick (onPress), onKeydown, onKeyup
* necessity to determine role (=button for DIVs)
* necessity to set tabIndex

<strong>After</strong>

* all the keyboard events are set automatically (basing on onClick for example)
* isPressed state is provided which makes it really easy to add styling
* 'role' and 'tabIndex' are set automatically

<strong>Not cool (?)</strong>
* it looks like default tabIndex is not supported, so technically we can't pass some default value, but maybe it's for good. In most cases, we don't need that.
* there may be conflicts in properties naming - for example, with these primitives we can't use prop 'type' on a Button component, cause 'button' tag has this property. Same for isPressed.

Overall I'm looking really forward to use other primitives - there are some really cool ones there. The whole product looks solid and trustworthy as well.
