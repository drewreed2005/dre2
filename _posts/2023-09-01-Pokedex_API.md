---
title: Pokédex API
layout: default
description: Using JavaScript, HTML and a free API to make a Pokémon database.
comments: true
hide: true
courses: { csa: {week: 2} }
type: hacks
---

# Pokédex API

Find your favorite Pokémon in the Pokédex!

Input a Pokémon: <input id="search_box" type="text" width="100"> <button id="search_button" onclick="fetchData()">Search</button>

<div id="big_container">
    <div id="base_data" class="info_container">
        <div id="images" class="info_container">
            <div id="image_n" class="poke_image">
                <!--NORMAL IMAGE HERE-->
                <figcaption>Normal Sprite</figcaption>
            </div>
            <div id="image_s" class="poke_image">
                <!--SHINY IMAGE HERE-->
                <figcaption>Shiny Sprite</figcaption>
            </div>
        </div>
        <div id="basic_info" style="width:340px;border: 2px solid white; margin: 5px;">
            <h3 id="poke_name_header" style="text-align: center;"><!--POKEMON NAME HERE--></h3><br>
            <h4 id="species_box" style="text-align: center;"></h4><br>
            <h5 id="typing_box" style="text-align: center;"></h5><br>
            <div id="pokedex_entry_box">
                <!--POKEDEX ENTRY HERE-->
            </div>
        </div>
    </div>
    <div id="stats_data" class="info_container" width="600"></div>
</div>

<style>
    #big_container {
        width: 600px;
        border: 2px solid white;
    }

    .info_container {
        display: flex;
        justify-content: space-around;
        border: 2px solid white;
        margin: 5px;
    }

    .poke_image {
        margin: 5px;
        width: 100px;
        text-align: center;
    }
</style>

<script>
    // pokemon name input
    const pokeSearch = document.getElementById("search_box");

    // establishing global variables
    var movesArray = [];
    var defaultData = [];
    var speciesData = [];
    var evolutionData = [];

    // function to fetch data based on user input
    function fetchData() {
        // prepare fetch options
        var url = "https://pokeapi.co/api/v2/pokemon/" + pokeSearch.value.toLowerCase();
        var options = {
            method: 'GET',
            mode: 'cors',
            cache: 'default',
            credentials: 'omit',
            headers: {
                'Content-Type': 'application/json'
            },
        };

        // fetch the API
        fetch(url, options)
            .then(response => {
                // check for response errors
                if (response.status !== 200) {
                    var errorMsg = 'Database response error: ' + response.status;
                    console.log(errorMsg);
                }
                // valid response will have JSON data
                response.json().then(data => {
                    console.log(data);
                    defaultData = data;
                    fetch(defaultData["species"]["url"], options).then(response => {
                        response.json().then(data => {
                            console.log(data);
                            speciesData = data;
                            fetch(speciesData["evolution_chain"]["url"], options).then(response => {
                                response.json().then(data => {
                                    console.log(data);
                                    evolutionData = data;

                                    // image boxes
                                    var pokeImgN = document.createElement("img"); // normal image
                                    var pokeImgS = document.createElement("img"); // shiny image
                                    pokeImgN.src = defaultData["sprites"]["front_default"]; // normal image source
                                    pokeImgS.src = defaultData["sprites"]["front_shiny"]; // shiny image source
                                    document.getElementById("image_n").appendChild(pokeImgN);
                                    document.getElementById("image_s").appendChild(pokeImgS);

                                    // basic data box
                                    var pokeName = defaultData["name"]; // lowercase name of pokemon
                                    var pokeId = defaultData["id"]; // pokedex id number
                                    for (entry of speciesData["genera"]) {
                                        if (entry["language"]["name"] == "en") {
                                            var pokeSpecies = entry["genus"];
                                            break;
                                        }
                                    }
                                    document.getElementById("poke_name_header").innerHTML = pokeName.toUpperCase() + " (#" + pokeId.toString() + ")";
                                    generateEntry();
                                })
                            })
                        })
                    })
                })
            })
            .catch(err => {
                console.error(err);
            });
    }

    function generateEntry() {
        var selecting = true;
        while (selecting) {
            var chosenEntry = speciesData["flavor_text_entries"][Math.floor(Math.random() * speciesData["flavor_text_entries"].length)];
            if (chosenEntry["language"]["name"] == "en") {
                selecting = false;
            }
        }
        var entryText = chosenEntry["flavor_text"].replace(/\n/g, ' ');
        console.log(entryText);
    }
    // fetchData();
</script>