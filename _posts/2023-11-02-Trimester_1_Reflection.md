---
toc: false
comments: true
layout: post
title: Trimester 1 Reflection
description: Reflections on the entire trimester.
courses: { csa: {week: 11} }
type: tangibles
---

## Code Contributions

### Overall

<img src="{{site.baseurl}}/images/Screen Shot 2023-11-03 at 9.48.21 AM.png">

I've been constantly at work in computer science since the beginning of the year. My commits have grown even more frequent recently.

My current efforts are toward making more <i>frequent</i> commits. I still haven't entirely abandoned the nasty habit of committing large amounts of code all at once.

### Group Contributions

I am the backend developer of DADDJ. As a result, I only made direct commits from my computer on the backend, although I did influence some frontend materials shown later in this blog. As you can see below, I committed quite a lot, with many lines of code.

<img src="{{site.baseurl}}/images/Screen Shot 2023-11-03 at 9.39.12 AM.png">

#### Early API Setup and Custom Java

Most of the framework of the Usr API (my primary section of the backend) came with [this commit](https://github.com/CSA-Tri-1/DADDJbackend/pull/3/commits/79c7a80abc61894f8d254f121e868fc2e5e2713a) in [this branch](https://github.com/CSA-Tri-1/DADDJbackend/pull/3/commits) (which has since been merged). This was mostly copying over information frmo the Person API and customizing the attributes slightly to fit the needs of the project.

This is where I first applied the Java we learned about in lessons. You can see some methods here that were remnants of the old idea to make the pathfinding algorithm usable for a game. We ran out of time to implement that. I later finalized the version of `getAverageScore()` seen below. This didn't end up making it into the project, but it showed work with objects early on.

```java
// a custom getter to return the average score of a usr
public double getAverageScore() {
    // check if the usr has played at all and that score total is valid compared to number of games
    if ((this.numberOfScores != 0) && ((this.numberOfScores * 100) > this.totalOfAllScores)) {
        return this.totalOfAllScores / this.numberOfScores;
    }
    return 0.0;
}
```

#### Exploring OOP

There was one specific experience in the process that I made sure to make a mental note of for this reflection blog. It happened during early efforts to save graph data to the user API in [this commit](https://github.com/CSA-Tri-1/DADDJbackend/pull/3/commits/d5d90c77be1ea8f2dff95dbdca69ab8e12cfffa5).

```java
public Usr(String email, String password, String name) { //double highScore, double totalOfAllScores, int numberOfScores) {
    this.email = email;
    this.password = password;
    this.name = name;
    this.canvasHistory = new ArrayList<HashMap<String, HashMap<String, Integer[][]>>>();
    // attributes below commented out (this was after we figured out that the game would be too much)
    // this.highScore = highScore;
    // this.totalOfAllScores = totalOfAllScores;
    // this.numberOfScores = numberOfScores;
}

// method shown in a little bit to add canvas history to the arraylist
public void addCanvasHistory(HashMap<String, HashMap<String, Integer[][]>> newCanvasHistory) {
        this.canvasHistory.add(newCanvasHistory);
    }
```

As you can see above, I initialized the canvasHistory attribute as a complicated ArrayList in the argument constructor. There was a problem though:

```java
Usr p3 = new Usr();
p3.setName("Drew Reed");
p3.setEmail("drewreedyo@gmail.com");
p3.setPassword("notMyActualPassw0rd");
/*
 * data variable is created (it's a canvas hashmap used for testing)
*/
p3.addCanvasHistory(data);
```

This didn't work. I was confused as to why; I had hardly changed the original Person code by that point. I finished my lesson on Inheritance for class and I ended up figuring it out: the Lombok-created no-argument constructor did not include a call to initialize the ArrayList of canvas history. From there, it was an easy fix:

```java
Usr p3 = new Usr("drewreedyo@gmail.com", "insecure", "Drew Reed");
// same process below
```

We eventually went on to change the data structure for the request made to add a graph, as well as many other aspects of it. After successfully creating the PUT request (see [this commit](https://github.com/CSA-Tri-1/DADDJbackend/compare/e47c0ee3892ad86d526ca92fc7a086f18352549c...d628559e6dbc8e009fa3cafb9d7431d5d4b55505)), I used my blog for personal testing. More on that later.

#### Dependencies and Controlling the API

Here are some smaller things I felt would be good to mention.

- There were lots of adjustments and workarounds made to SecurityConfig.java and MvcConfig.java

```java
// Provide security configuration
@Override
protected void configure(HttpSecurity httpSecurity) throws Exception {
    httpSecurity
        // no CSRF
        .csrf().disable()
        // list the requests/endpoints need to be authenticated
        .authorizeRequests()
            .antMatchers("/mvc/usr/update/**", "/mvc/usr/delete/**").authenticated()
            //.antMatchers("/api/usr/**").permitAll()
            .antMatchers("/api/usr/", "/api/usr/delete").authenticated()
            .antMatchers("/api/usr/post", "/api/usr/update", "/authenticate").permitAll()
        // ...
        .addHeaderWriter(new StaticHeadersWriter("Access-Control-Allow-Origin", "http://localhost:4200", "http://localhost:4000", "https://drewreed2005.github.io", "https://csa-tri-1.github.io", "https://daddjbackend.stu.nighthawkcodingsociety.com")) // mostly testing urls
```

- When ensuring that direct JSON data from the frontend could be converted to data, before using the eventual solution that was built into the Springboot functionality, I worked with GSON and got it to function correctly. These were new dependencies and use of libraries in Java.

```xml
<dependency>
    <groupId>com.google.code.gson</groupId>
    <artifactId>gson</artifactId>
    <version>2.10.1</version> <!-- Use the latest version available -->
</dependency>
```

```java
import com.google.gson.Gson;
import com.google.gson.reflect.TypeToken;
//...this one had some help from ChatGPT
public static HashMap<String, HashMap<String, Integer[][]>> convertJsonToData(String jsonData) {
    Gson gson = new Gson();
    // type definition for parsing
    TypeToken<HashMap<String, HashMap<String, Integer[][]>>> typeToken =
            new TypeToken<HashMap<String, HashMap<String, Integer[][]>>>() {
            };
    // parsing JSON into correct data structure
    HashMap<String, HashMap<String, Integer[][]>>
            data = gson.fromJson(jsonData, typeToken.getType());

    return data;
}
```

- There was an error in the original Person API that made it so that proper POST requests wouldn't encrypt the given passwords. I eventually tried solving it by commiting changes straight to UserDetailsService, but it ended up causing overall problems when deleting these users (likely related to expected user data being cached in UserDetailsService for some reason). I figured out an actual fix that you can see works now. I bet a lot of other groups didn't figure this out.

```java
@PostMapping( "/post")
    public ResponseEntity<Object> postUsr(@RequestParam("email") String email,
                                            @RequestParam("password") String password,
                                            @RequestParam("name") String name) {
        // A usr object WITHOUT ID will create a new record with default roles as student
        Usr usr = new Usr(email, password, name);
        usr.setPassword(passwordEncoder.encode(usr.getPassword())); // PASSWORD ENCRYPTION
        repository.save(usr);
        return new ResponseEntity<>(email +" is created successfully", HttpStatus.CREATED);
    }
```

## Blog Usage

### Score Checks and Corrections

See my College Board test score and reflections [here](https://drewreed2005.github.io/dre2//2023/11/03/CB_Quiz_Tri_1.html).

See my scores in previous student lessons [here](https://drewreed2005.github.io/dre2//2023/11/03/Student_Scores_Tri_1.html).

### Planning

Open up the [Time Box](https://drewreed2005.github.io/dre2/csa) and see the up-to-date planning blogs accessible each week.

### Use of Blog for Java Contributions

You can see blogging of various group objectives throughout my [Time Box](https://drewreed2005.github.io/dre2/csa), but here are a few specific examples.

#### Usr Database Connection Test

[This page](https://drewreed2005.github.io/dre2//2023/10/15/Usr_Database_Connection.html) is the reason I gave my blog Access-Control-Allow-Origin access on the backend. To avoid having to use the frontend site directly for testing, especially since it was constantly being modified and fixed by our frontend guys, I used this page for testing.

It does fetch requests to /authenticate, /post (user creation), /get (see console) and /update (earlier stages). It was integral in getting them all to work properly with frontend data. It's also where I found that the "JSON failed to map" errors were causing problems with the update functionality.

#### Dijkstra Page

[This page](https://drewreed2005.github.io/dre2//2023/09/18/Dijkstra-Algorithm_IPYNB_2_.html) acted as the basis for adding Dijkstra to the backend. Alex was primarily responsible for figuring out how to get the algorithm to work, particularly with frontend JSON data, but we both worked together to implement the API connection because I had figured it out with the Usr database by that time.

#### Working Usr Graph Data Object

[This page](https://drewreed2005.github.io/dre2//2023/10/26/Canvas_History_Body_Object_IPYNB_2_.html) logs an attempt to get the graph data to map to JSON using `RequestBody`. This was based on something that Alex had figured out about the Springboot file mechanics with object declarations. This ended up being the winning solution, although I had to take the unique setter out of the constructor in the final version (partially because of the shift in data types).

## Overall Reflection

In this trimester, I got a taste of just how much more there was to know about computer science. Very cliche.

I've never worked with an object-oriented programming language before, though I've worked with objects a lot since last year so it wasn't very uncomfortable. The biggest jump, aside from syntax, was variable and method type and scope declarations. I had a lot of pain dealing with variable scope earlier last year, but during this year, I've really understood it.

The beginning of this class overall has been a big jump in my way of handling the class and the subject. Last year, I was constantly crippled by a fundamental lack of understanding of what was going on. I didn't really understand how the things I was working with were working on a base level. This year, however, having gone in with some amount of deeper understanding of the inner workings of these systems has made it a lot easier to pick up on and run with new concepts.

As an overall reflection of the good and bad, though, here's a table.

<table>
    <thead>
        <tr>
            <th>Glows</th>
            <th>Grows</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>
                <ul>
                    <li>Good understanding of concepts guiding work</li>
                    <li>All code referenced from other sources (including ChatGPT) has been well-understood</li>
                    <li>Good extension of and work with Agile roles and work organization (see Scrum Board)</li>
                    <li>Blog use has been frequent and helpful to workflow</li>
                    <li>Particularly strong grasp of Java syntax in a relatively short time</li>
                    <li>Thanks to new group members to learn from, there's a better understanding of GitHub features and concepts that are conducive to better-organized groupwork</li>
                </ul>
            </td>
            <td>
                <ul>
                    <li>Occasional lack of punctuality (example: this)</li>
                    <li>Specificity of initial goal with project led to being boxed in</li>
                    <li>Better communcation with frontend could lead to having features implemented more clearly/ideally</li>
                    <li>Work on project doesn't show full extent of understanding (could have been expanded)</li>
                    <li>Engagement in class lessons could be increased</li>
                </ul>
            </td>
        </tr>
    </tbody>
</table>