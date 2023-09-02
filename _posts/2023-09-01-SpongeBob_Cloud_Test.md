---
title: SpongeBob Cloud Test
layout: default
description: Trying to make it work for my whole site.
comments: true
hide: true
courses: { csa: {week: 2} }
type: hacks
---

## Sample Header

This is sample text to see how it looks when text is included as well. There could be lots and lots of text to make this situation better. There are all sorts of things this could say, and there are even code blocks and bullets that could get in front.
- The bullets will look like this.
    - The subbullets are like this.
        - And there are even sub-subbullets. Crazy, huh?
- More bullet thing.
- More, even.
- Even more.

### Subheader

Check this out, dude.

<div class="cloud-container">
    <img src="{{site.baseurl}}/images/flower-clouds/blue_flower.png" alt="SpongeBob Cloud" class="cloud">
</div>

<style>

.cloud-container {
    position: absolute;
    top: 50%;
    left: -100px; /* Start off the screen on the left */
    transform-origin: center;
    animation: moveRotate 40s linear infinite, blur 40s linear infinite alternate;
}

.cloud {
    width: 25vw;
    height: auto;
    opacity: 0.9; /* adjust opacity as needed */
    transition: filter 0.5s ease; /* smoothly transition blur effect */
}

@keyframes moveRotate {
    0% {
        transform: translateX(-20vw) rotate(0deg);
    }
    100% {
        transform: translateX(125vw) rotate(360deg);
    }
}

@keyframes blur {
    0% {
        filter: blur(2px);
    }
    100% {
        filter: blur(5px); /* adjust blur amount as needed */
    }
}
</style>

<script>
    // Get a reference to the body element
    var bodyHeight = document.body.clientHeight;
</script>