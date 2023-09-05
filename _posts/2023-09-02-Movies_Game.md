---
title: Movies Game
layout: default
description: Try to guess between the stats of different movies!
comments: true
hide: true
courses: { csa: {week: 3} }
type: hacks
---

# Movies Game

Try to guess between the states of different movies!

<script>
    const options = {method: 'GET', headers: {accept: 'application/json'}};

    fetch('https://api.themoviedb.org/3/movie/movie_id?language=en-US', options)
    .then(response => response.json())
    .then(response => console.log(response))
    .catch(err => console.error(err));
</script>