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

<table id="movies_container" style="border: 2px solid white;display:flex; width: 600px">
    <tr>
        <td id="movie0" style="width: 300px;"></td>
        <td id="movie1" style="width: 300px;"></td>
    </tr>
</table>
<div id="timer"></div>

<script>
    // disable flowers
    flowerDisable = true;

    const options = {
        method: 'GET',
        headers: {
            accept: 'application/json',
            Authorization: 'Bearer eyJhbGciOiJIUzI1NiJ9.eyJhdWQiOiJmYzdmYzdmMTkzOWNlYWZjODA3MzZiZDUwMmQ1MDc1YSIsInN1YiI6IjY0ZjY3MGRlMTIxOTdlMDEzOGI1ZGFkOSIsInNjb3BlcyI6WyJhcGlfcmVhZCJdLCJ2ZXJzaW9uIjoxfQ.jTFa6OHmAJBJOfHxI8XxCqaHxSbH1X0y4AKHlIsaI_Y'
        }
    };

    function rng(max) {
        return Math.floor(Math.random() * max);
    }

    const firstMovie = document.getElementById("movie0");
    const secondMovie = document.getElementById("movie1");
    var currentMovies = [];
    var currentMovieScores = [];
    var playerScore = 0;
    var currentStreak = 0;

    function generateRandomMovie() {
        currentMovies = [];
        for (var i = 0; i < 2; i++) {
            j = 0;
            console.log(j);
            var selectedPage = String(rng(500) + 1);
            var movieIndex = rng(20);
            fetch('https://api.themoviedb.org/3/discover/movie?api_key=fc7fc7f1939ceafc80736bd502d5075a&include_adult=false&language=en-US&page=' + selectedPage, options)
                .then(response => response.json())
                .then(response => {
                    console.log(response);
                    console.log(response["results"][movieIndex]);
                    var selectedMovie = response["results"][movieIndex];
                    currentMovies.push(selectedMovie);
                    var moviePoster = document.createElement("img");
                    moviePoster.src = "https://image.tmdb.org/t/p/w300/" + selectedMovie["poster_path"];
                    console.log(j);
                    moviePoster.setAttribute("onclick", "selectMovie(" + String(j) + ")");
                    j++;
                    if (j < 2) {
                        firstMovie.appendChild(moviePoster);
                    } else {
                        secondMovie.appendChild(moviePoster);
                    }
                })
            .catch(err => console.error(err));
        }
    }

    function timerInit() {
        
    }

    function selectMovie(int) {
        console.log(int);
        currentMovieScores = [];
        for (movie of currentMovies) {
            currentMovieScores.push(movie["vote_average"]);
        }
        console.log(currentMovieScores);
        if (currentMovieScores[int] > currentMovieScores[(int + 1) % 2]) {
            console.log("Congratulations! You chose the higher-rated movie.");
        } else if (currentMovieScores[int] < currentMovieScores[(int + 1) % 2]) {
            console.log("Oh no! The movie you chose wasn't as highly rated.");
        } else {
            console.log("It looks like they're tied! Lucky--you get free points!");
        }
    }

    generateRandomMovie();
</script>