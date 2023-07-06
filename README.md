This repository contains the HTML and JavaScript code for the Flatdango Movie Ticket Booking website.

HTML Code
html
Copy code
<!DOCTYPE html>
<html lang="en">
<head>
    <link rel="stylesheet" href="index.css">
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Flatdango Movie Ticket Booking</title>
</head>
<body>
    <header>
        Flatdango Movie Tickets
    </header>
    <div id="movie-information">
        <h2 id="title">title</h2>
        <p id="run-time">Runtime:</p>
        <p id="show-time">Showtime:</p>
        <p id="tickets">Tickets:</p>
        <button id="buy-ticket">BuyTicket</button>
    </div>

    <div id="movie-details">
        <div id="poster-container">
            <img id="poster" src="darius" alt="Movie Poster,darius">
        </div>
    </div>

    <div id="movie-menu">
        <h1>Menu</h1>
        <ul id="films"></ul>
    </div>

    <script src="index.js"></script>
</body>
</html>
JavaScript Code
javascript
Copy code
document.addEventListener("DOMContentLoaded", () => {
  var selectedMovieId = localStorage.getItem("selectedMovieId");
  fetch("http://localhost:3000/films")
    .then(response => response.json())
    .then(movies => {
      const filmsList = document.getElementById("films");
      movies.forEach(movie => {
        const li = document.createElement("li");
        li.classList.add("film", "item");
        li.textContent = movie.title;
        li.addEventListener("click", () => {
          localStorage.setItem("selectedMovieId", movie.id);
          fetch(`http://localhost:3000/films/${movie.id}`)
            .then(response => response.json())
            .then(movieDetails => {
              var buyTicketBtn = document.getElementById("buy-ticket");
              var movieTitle = document.getElementById("title");
              var movieRuntime = document.getElementById("run-time");
              var moviePoster = document.getElementById("poster");
              var movieTicketsAvailable = document.getElementById("tickets");
              var movieShowtime = document.getElementById("show-time");
              movieTitle.textContent = movieDetails.title;
              moviePoster.src = movieDetails.poster;
              movieShowtime.textContent = `Showtime: ${movieDetails.showtime}`;
              movieRuntime.textContent = `Runtime: ${movieDetails.runtime} minutes`;
              var ticketsAvailable = movieDetails.capacity - movieDetails.tickets_sold;
              movieTicketsAvailable.textContent = `Tickets Available: ${ticketsAvailable}`;
              buyTicketBtn.disabled = ticketsAvailable === 0;
              buyTicketBtn.addEventListener("click", event => {
                event.preventDefault();
                if (ticketsAvailable > 0) {
                  fetch(`http://localhost:3000/films/${movie.id}`, {
                    method: "PATCH",
                    headers: {
                      "Content-Type": "application/json"
                    },
                    body: JSON.stringify({
                      tickets_sold: movieDetails.tickets_sold + 1
                    })
                  })
                    .then(response => response.json())
                    .then(updatedMovieDetails => {
                      movieDetails = updatedMovieDetails;
                      ticketsAvailable = movieDetails.capacity - movieDetails.tickets_sold;
                      movieTicketsAvailable.textContent = `Tickets Available: ${ticketsAvailable}`;
                      if (ticketsAvailable === 0) {
                        buyTicketBtn.disabled = true;
                        buyTicketBtn.textContent = "Sold Out";
                      }
                    })
                    .catch(error => {
                      console.log("Error updating ticket count:", error);
                    });
                }
              });
            })
            .catch(error => {
              console.log("Error fetching movie details:", error);
            });
        });
        if (selectedMovieId && movie.id === selectedMovieId) {
          li.click();
        }
        filmsList.appendChild(li);
      });
    })
    .catch(error => {
      console.log("Error fetching movie menu:", error);
    });
});
Description
This code represents a movie ticket booking website called Flatdango. It allows users to view and select movies, check movie details, and purchase tickets.

The HTML code provides the structure of the webpage, including the header, movie information section, movie details section, movie menu section, and script tag to link the JavaScript code.

The JavaScript code is responsible for fetching movie data from a server, populating the movie menu, updating movie details when a movie is selected, and handling the ticket purchase functionality. It uses the fetch API to communicate with the server and updates the DOM elements dynamically based on the retrieved data.

To run this code, make sure you have the necessary server endpoints available and adjust the URLs in the JavaScript code accordingly.

Feel free to explore and modify the code to suit your needs. Enjoy the movie ticket booking experience with Flatdango!