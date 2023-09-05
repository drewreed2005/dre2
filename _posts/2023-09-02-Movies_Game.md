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
    const options = {
        method: 'GET',
        headers: {
            accept: 'application/json',
            Authorization: 'Bearer eyJhbGciOiJIUzI1NiJ9.eyJhdWQiOiJmYzdmYzdmMTkzOWNlYWZjODA3MzZiZDUwMmQ1MDc1YSIsInN1YiI6IjY0ZjY3MGRlMTIxOTdlMDEzOGI1ZGFkOSIsInNjb3BlcyI6WyJhcGlfcmVhZCJdLCJ2ZXJzaW9uIjoxfQ.jTFa6OHmAJBJOfHxI8XxCqaHxSbH1X0y4AKHlIsaI_Y'
        }
    };

    fetch('https://api.themoviedb.org/3/authentication', options)
        .then(response => response.json())
        .then(response => console.log(response))
        .catch(err => console.error(err));
</script>