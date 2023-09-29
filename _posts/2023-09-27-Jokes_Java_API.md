---
title: Custom Survey API Pull
layout: default
description: An API pull from a blog-based local backend to show understanding of JPA.  
courses: { csa: {week: 6} }
type: hacks
---

<!-- HTML table fragment for page -->
<table>
  <thead>
  <tr>
    <th>Joke</th>
    <th>Like</th>
    <th>Dislike</th>
  </tr>
  </thead>
  <tbody id="result">
    <!-- javascript generated data -->
  </tbody>
</table>

<!-- Script is layed out in a sequence (without a function) and will execute when page is loaded -->
<script>

  // prepare HTML defined "result" container for new output
  const resultContainer = document.getElementById("result");

  // keys for joke reactions
  const AGREE = "agree";
  const DISAGREE = "disagree";
  const NEITHER = "neither";

  // prepare fetch urls
  // const url = "https://flask.nighthawkcodingsociety.com/api/jokes";
  const url = "http://0.0.0.0:8085/api/survey"; //https://spring.nighthawkcodingsociety.com/api/jokes
  const get_url = url +"/";
  const agr_url = url + "/agree/";  // agree reaction
  const dis_url = url + "/disagree/";  // disagree reaction
  const nei_url = url + "/neither/";  // neither reaction

  // prepare fetch GET options
  const options = {
    method: 'GET', // *GET, POST, PUT, DELETE, etc.
    mode: 'cors', // no-cors, *cors, same-origin
    cache: 'default', // *default, no-cache, reload, force-cache, only-if-cached
    credentials: 'same-origin', // include, same-origin, omit
    headers: {
      'Content-Type': 'application/json'
      // 'Content-Type': 'application/x-www-form-urlencoded',
    },
  };
  // prepare fetch PUT options, clones with JS Spread Operator (...)
  const post_options = {...options,
    method: 'POST',
    mode: 'cors', // no-cors, *cors, same-origin
  }; // clones and replaces method

  // fetch the API
  fetch(get_url, options)
    // response is a RESTful "promise" on any successful fetch
    .then(response => {
      // check for response errors
      if (response.status !== 200) {
          error('GET API response failure: ' + response.status);
          return;
      }
      // valid response will have JSON data
      response.json().then(data => {
          console.log(data);
          var i = 0;
          for (const row of data) {
            // increment id variable
            i++;

            // make "tr element" for each "row of data"
            const tr = document.createElement("tr");
            
            // td for joke cell
            const survey = document.createElement("td");
              survey.innerHTML = String(i) + ". " + row.survey;  // add fetched data to innerHTML

            // td for haha cell with onclick actions
            const agree = document.createElement("td");
              const agree_but = document.createElement('button');
              agree_but.id = AGREE+row.id   // establishes a HAHA JS id for cell
              agree_but.innerHTML = row.agree;  // add fetched "haha count" to innerHTML
              agree_but.onclick = function () {
                // onclick function call with "like parameters"
                reaction(AGREE, agr_url+row.id, agree_but.id);  
              };
              agree.appendChild(agree_but);  // add "haha button" to haha cell

            // td for boohoo cell with onclick actions
            const disagree = document.createElement("td");
              const disagree_but = document.createElement('button');
              disagree_but.id = DISAGREE+row.id  // establishes a BOOHOO JS id for cell
              disagree_but.innerHTML = row.disagree;  // add fetched "boohoo count" to innerHTML
              disagree_but.onclick = function () {
                // onclick function call with "jeer parameters"
                reaction(DISAGREE, dis_url+row.id, disagree_but.id);  
              };
              disagree.appendChild(disagree_but);  // add "boohoo button" to boohoo cell

            // td for boohoo cell with onclick actions
            const neither = document.createElement("td");
              const neither_but = document.createElement('button');
              neither_but.id = NEITHER+row.id  // establishes a BOOHOO JS id for cell
              neither_but.innerHTML = row.neither;  // add fetched "boohoo count" to innerHTML
              neither_but.onclick = function () {
                // onclick function call with "jeer parameters"
                reaction(NEITHER, nei_url+row.id, neither_but.id);  
              };
              neither.appendChild(neither_but);  // add "boohoo button" to boohoo cell
             
            // this builds ALL td's (cells) into tr (row) element
            tr.appendChild(joke);
            tr.appendChild(agree);
            tr.appendChild(disagree);
            tr.appendChild(neither);

            // this adds all the tr (row) work above to the HTML "result" container
            resultContainer.appendChild(tr);
          }
      })
  })
  // catch fetch errors (ie Nginx ACCESS to server blocked)
  .catch(err => {
    error(err + ": " + get_url);
  });

  // Reaction function to likes or jeers user actions
  function reaction(type, post_url, elemID) {

    // fetch the API
    fetch(post_url, post_options)
    // response is a RESTful "promise" on any successful fetch
    .then(response => {
      // check for response errors
      if (response.status !== 200) {
          error("Post API response failure: " + response.status)
          return;  // api failure
      }
      // valid response will have JSON data
      response.json().then(data => {
          console.log(data);
          // Likes or Jeers updated/incremented
          if (type === AGREE) // like data element
            document.getElementById(elemID).innerHTML = data.agree;  // fetched haha data assigned to haha Document Object Model (DOM)
          else if (type === DISAGREE) // jeer data element
            document.getElementById(elemID).innerHTML = data.disagree;  // fetched boohoo data assigned to boohoo Document Object Model (DOM)
          else if (type === NEITHER)
            document.getElementById(elemID).innerHTML = data.neither;
          else
            error("unknown type: " + type);  // should never occur
      })
    })
    // catch fetch errors (ie Nginx ACCESS to server blocked)
    .catch(err => {
      error(err + " " + post_url);
    });
    
  }

  // Something went wrong with actions or responses
  function error(err) {
    // log as Error in console
    console.error(err);
    // append error to resultContainer
    const tr = document.createElement("tr");
    const td = document.createElement("td");
    td.innerHTML = err;
    tr.appendChild(td);
    resultContainer.appendChild(tr);
  }

</script>