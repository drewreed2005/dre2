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

<div id="points_counter">Your points: 0</div>
<div id="streak_counter">Your streak: 0</div>
<button id="movie_generator" onclick="generateRandomMovies()">Generate Movie</button>

<table id="movies_container" style="border: 2px solid white;display:flex; width: 600px; position: relative;">
    <tr>
        <td id="movie0" style="width: 300px;">
            <div id="results0" style="position: absolute; top: 150px; left: 20%; font-size: 48px; text-shadow: 2px 2px 2px rgba(0, 0, 0, 0.9); z-index: 11;"></div>
        </td>
        <td id="movie1" style="width: 300px;">
            <div id="results1" style="position: absolute; top: 150px; left: 70%; font-size: 48px; text-shadow: 2px 2px 2px rgba(0, 0, 0, 0.9); z-index: 11;"></div>
        </td>
    </tr>
</table>
<div id="results"></div>
<div id="timer"></div>

<img src="{{site.baseurl}}/images/cursors/paint_cursor.png" width="100"><br>
<button id="cursor_shop0" onclick="cursorPurchase(10000, 0)">Buy Paint Cursor (10,000 score)</button>

<img src="{{site.baseurl}}/images/cursors/mort_cursor.png" width="100"><br>
<button id="cursor_shop1" onclick="cursorPurchase(100000, 1)">Buy Mort Cursor (100,000 score)</button>

<script>
    // disable flowers
    flowerDisable = true;

    // consts
    const results = document.getElementById("results");
    const firstMovie = document.getElementById("movie0");
    const secondMovie = document.getElementById("movie1");
    const timerBox = document.getElementById("timer");
    const cursorPaths = ["paint_cursor", "mort_cursor"];
    let timerVar; //global for timer stopping/starting
    var currentTime = 10; // global time
    var currentMovies = [];
    var currentMovieScores = [];
    var playerScore = 0;
    var currentStreak = 0;

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

    function generateRandomMovies() {
        // deleting old images if applicable
        try {
            for (let i = 0; i < 2; i++) {
                document.getElementById('results' + String(i)).innerHTML = "";
                document.getElementById('poster' + String(i)).remove();
            }
        } catch (err) {}

        // results text
        results.innerHTML = "Select the better-received film!";

        currentMovies = [];
        for (let i = 0; i < 2; i++) {
            j = 0;
            console.log(j);
            var selectedPage = String(rng(500) + 1);
            var movieIndex = rng(20);
            fetch('https://api.themoviedb.org/3/discover/movie?api_key=fc7fc7f1939ceafc80736bd502d5075a&include_adult=false&language=en-US&original_language=&page=' + selectedPage, options)
                .then(response => response.json())
                .then(response => {
                    console.log(response);
                    console.log(response["results"][movieIndex]);
                    var selectedMovie = response["results"][movieIndex];
                    currentMovies.push(selectedMovie);
                    var moviePoster = document.createElement("img");
                    moviePoster.src = "https://image.tmdb.org/t/p/w300/" + selectedMovie["poster_path"];
                    moviePoster.setAttribute("id", "poster" + String(j));
                    console.log(j);
                    moviePoster.setAttribute("onclick", "selectMovie(" + String(j) + ", false)");
                    j++;
                    if (j < 2) {
                        firstMovie.appendChild(moviePoster);
                    } else {
                        secondMovie.appendChild(moviePoster);
                    }
                })
            .catch(err => console.error(err));
        }
        timerSet(true);
    }

    function timerSet(on) {
        console.log(timerVar);
        if (on) {
            currentTime = 10;
            timerVar = setInterval(() => {
                currentTime -= 0.1; // subtract 0.1 second
                currentTime = Math.floor(currentTime * 10) / 10; // rounding for floating point error fixing
                timerBox.innerHTML = "Time remaining: " + String(currentTime) + " seconds";
                if (currentTime <= 0) {
                    console.log("Time's up!");
                    selectMovie(0, true);
                }
            }, 100); // Run every 100 milliseconds (0.1 seconds)
        } else {
            clearInterval(timerVar);
        }
    }

    function updateScore() {
        document.getElementById("points_counter").innerHTML = "Your points: " + String(playerScore);
        document.getElementById("streak_counter").innerHTML = "Your streak: " + String(currentStreak);
    }

    function selectMovie(int, timer) {
        console.log(timerVar);
        timerSet(false); // stop the interval
        console.log(int);
        currentMovieScores = [];

        for (movie of currentMovies) {
            currentMovieScores.push(movie["vote_average"]);
        }
        for (let i = 0; i < 2; i++) {
            document.getElementById("poster" + String(i)).style["filter"] = "blur(3px)";
            document.getElementById("poster" + String(i)).setAttribute("onclick", "");
            document.getElementById("results" + String(i)).innerHTML = currentMovieScores[i];
        }
        console.log(currentMovieScores);
        if (timer) {
            currentStreak = 0;
            console.log("Time's up! You lose 300 points for not guessing on time.");
            results.innerHTML = "Time's up! You lose 300 points for not guessing on time. (-300 score)";
            if (playerScore >= 300) {
                playerScore -= 300;
            } else {
                playerScore = 0;
            }
            updateScore();
            return;
        }
        if (currentMovieScores[int] > currentMovieScores[(int + 1) % 2]) {
            console.log("Congratulations! You chose the higher-rated movie.");
            currentStreak += 1;
            var newScore = Math.floor((1000 * (currentTime / 10)) * (1 + (currentStreak * 0.1)));
            playerScore += newScore;
            results.innerHTML = "Congratulations! You chose the higher-rated movie. (+" + String(newScore) + " score)";
        } else if (currentMovieScores[int] < currentMovieScores[(int + 1) % 2]) {
            console.log("Oh no! The movie you chose wasn't as highly rated.");
            results.innerHTML = "Oh no! The movie you chose wasn't as highly rated. (-250 score)";
            currentStreak = 0;
            if (playerScore >= 250) {
                playerScore -= 250;
            } else {
                playerScore = 0;
            }
        } else {
            console.log("It looks like they're tied! Lucky--you get free points!");
            results.innerHTML = "It looks like they're tied! Lucky--you get free points! (+500)";
            currentStreak += 1;
            playerScore += 500;
        }
        updateScore();
    }

    function cursorPurchase(cost, whichCursor) {
        if (playerScore >= cost) {
            playerScore -= cost;
            updateScore();
            document.body.style.cursor = 'url("/dre2/images/cursors/' + cursorPaths[whichCursor] + '.cur"), auto';
            results.innerHTML = "Thanks for your purchase!";
        } else {
            results.innerHTML = "You're too poor to buy that!";
        }
    }
</script>