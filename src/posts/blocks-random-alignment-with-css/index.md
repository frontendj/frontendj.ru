---
title: Blocks' random alignment with CSS
date: 2020-10-01
tags:
    - CSS
layout: post.njk
---

<p>Recently I had a nice CSS challenge - easy at first sight, but as usual, it happens, a bit tricky.</p>

<p>I needed to create a simple component in which several text blocks (let's call them tags) are displayed at random order. Something like that.</p>

<div class="snippet">
        <p class="codepen" data-height="265" data-theme-id="light" data-default-tab="css,result" data-user="frontendj" data-slug-hash="KKzzorz" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="CSS random align - 1 - static blocks">
      <span>See the Pen <a href="https://codepen.io/frontendj/pen/KKzzorz">
      CSS random align - 1 - static blocks</a> by Dmitry (<a href="https://codepen.io/frontendj">@frontendj</a>)
      on <a href="https://codepen.io">CodePen</a>.</span>
        </p>
</div>

<p>The initial markup was simple, and the result was quite boring. Considering that actually all the blocks weren't related to each other, there was a feeling, that they needed some dynamic, and I wanted to move them randomly in different directions. And here lied the challenge. I didn't know widths of blocks (cause inner text was random), I didn't know the width of the parent (cause markup was responsive), plus some blocks took 100% of the parent's width, so I couldn't move them at all.</p>

<p>Ideally, I wanted to calculate somehow the random length of a shift for every text block.</p>

``` css
.item {
    left: random(0, [parentWidth] - [itemWidth])
}
```

<p>SCSS has a built-in function for randomizing, but we can't mix there absolute and relative dimensions, and anyway, the result would be static. So it seemed that nothing could be solved in that direction.</p>

<p>I could try to generate just a random number within some reasonable range and try to shift the text blocks with its help. But in this case, I would have to adjust the maximum width for the blocks, otherwise, they will go beyond the boundaries of the parent.</p>

``` css
@for $i from 1 through 4 {
    .item:nth-child(#{$i}) {
        $random: random(30) - 15; // getting a shift between -15px and 15px
        margin-left: $random + px;
        max-width: calc(100% - #{2 * abs($random)}px);
    }
}
```

<div class="snippet">
      <p class="codepen" data-height="265" data-theme-id="light" data-default-tab="css,result" data-user="frontendj" data-slug-hash="PoNNRLr" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="CSS random align - 2 -  SCSS random">
      <span>See the Pen <a href="https://codepen.io/frontendj/pen/PoNNRLr">
      CSS random align - 2 -  SCSS random</a> by Dmitry (<a href="https://codepen.io/frontendj">@frontendj</a>)
      on <a href="https://codepen.io">CodePen</a>.</span>
      </p>
</div>

<p>Anyway, I tried and it seemed to work. But for long text blocks, some of the inner data were trimmed, because I had to compensate their shift with decreasing their width. In such cases, I'd like to just leave blocks without shifting. Luckily some flexbox magic came to help.</p>

<p>If we add blocks of random width before and after each tag, they will push and pull, and therefore balance the tag randomly. Also, they will shrink to zero width if the text block is too wide.</p>

``` css
.item {
    display: flex;

    &::before,
    &::after {
        content: '';
        flex: 1 1 auto;
        background: red;
        max-width: 40%;
    }

    &::after {
        background: green;
    }
}

@for $i from 1 through 4 {
    .item:nth-child(#{$i}) {
        &::before {
            flex-grow: random(5);
        }

        &::after {
            flex-grow: random(5);
        }
    }
}
```

<p>It works! Long tags displace balancers, and short tags are randomly aligned.</p>

<div class="snippet">
      <p class="codepen" data-height="265" data-theme-id="light" data-default-tab="css,result" data-user="frontendj" data-slug-hash="oNxxdgQ" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="CSS random align - 3 - pushers and pullers">
      <span>See the Pen <a href="https://codepen.io/frontendj/pen/oNxxdgQ">
      CSS random align - 3 - pushers and pullers</a> by Dmitry (<a href="https://codepen.io/frontendj">@frontendj</a>)
      on <a href="https://codepen.io">CodePen</a>.</span>
      </p>
</div>

<p>I could stop at this, but still I would like to add some real dynamics to make the text blocks appear with a random shift every time the block is redrawn, and not just when the CSS is recompiled. So a little bit of javascript would be fine:</p>

``` js
[...document.getElementsByClassName('item')].forEach((item) => {
      item.style.setProperty('--puller-grow', Math.random() * 5);
      item.style.setProperty('--pusher-grow', Math.random() * 5);
});
```

<div class="snippet">
      <p class="codepen" data-height="265" data-theme-id="light" data-default-tab="css,result" data-user="frontendj" data-slug-hash="JjXXvRm" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="CSS random align - 4 - JS random">
      <span>See the Pen <a href="https://codepen.io/frontendj/pen/JjXXvRm">
      CSS random align - 4 - JS random</a> by Dmitry (<a href="https://codepen.io/frontendj">@frontendj</a>)
      on <a href="https://codepen.io">CodePen</a>.</span>
      </p>
</div>

<p>Now everything is fine. Blocks are randomly arranged, stay within the parent's bounds, and make the most of the available space to display their content.</p>

<p>I stopped here, cause the font that I used, didn't give me the possibility to add some real dynamic to the blocks, and let them slowly slide back and forth. But I was surprised, that it actually can be done with changing flex-basis property. The result isn't spectacular though.</p>

<div class="snippet">
      <p class="codepen" data-height="265" data-theme-id="light" data-default-tab="css,result" data-user="frontendj" data-slug-hash="WNwoxgL" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="CSS random align - 5 - flex-basis animation">
      <span>See the Pen <a href="https://codepen.io/frontendj/pen/WNwoxgL">
      CSS random align - 5 - flex-basis animation</a> by Dmitry (<a href="https://codepen.io/frontendj">@frontendj</a>)
      on <a href="https://codepen.io">CodePen</a>.</span>
      </p>
      <script async src="https://static.codepen.io/assets/embed/ei.js" crossorigin></script>
</div>
