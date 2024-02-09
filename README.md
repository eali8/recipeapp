<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>API Recipe - Emmy Ali</title>

    <!-- Included bootstrap for styling -->
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css">
    <!-- Included custom CSS for additional styling -->
    <link rel="stylesheet" type="text/css" href="style.css">
</head>
<body>
   <header class="bg-dark text-white text-center py-3">
    <h1 class="header-title">API Recipes - Emmy Ali</h1>
    </header>

    
    <!-- Search container for getting user's input -->
    <div class="container-fluid" id="form-container">
        <h1 id="recipe-header">Recipes</h1>

        <!-- Description for the user of how to search -->
        <p class="text-center">Take a look for different recipes by entering your item into the search box. <br> Enter words like "Beef," "Tacos," "Quiche," or "Dessert" and see what the Meal DB database has to offer with a link to the recipe.</p>

        <!-- Search form -->
        <div class="form mb-4">
            <label for="recipe-search" id="recipe-label"><b>Search for Recipes</b></label>
            <input type="text" name="recipe-search" id="recipe-search" placeholder="Enter a recipe or meal"> 
            <input type="button" value="Search" id="searchBtn" onclick="loadRecipes()">
        </div>
        <br>
        <br>
        <!-- Container for recipe results -->
        <div class="container" id="recipe-rows-container"></div>

        <!-- Spinner for searching, the container will be cleared each time the user searches -->
        <div class="text-center">
            <div id="spinner"></div>
        </div>
    </div>

    <!-- Include jQuery -->
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.7.1/jquery.min.js"></script>

    <!-- Global variable for the user's input into the search box -->
    <script>
        var searchItem = '';

        // This function will load all our recipes from the API
        function loadRecipes() {
            // Call function to clear the recipes from the previous search
            clearRecipes();

            // Add spinner class to div while the search is completing
            $('#spinner').addClass('spinner-border');

            // Global variable of the user's input
            searchItem = $("#recipe-search").val();

            // AJAX call including the user's input -> searchItem
            $.ajax('https://www.themealdb.com/api/json/v1/1/search.php?s=' + searchItem)
            .done(function(result) {
                addToContainer(result);
            })
            .fail(function(xhr, status, error) {
                console.log("Error: " + error);
            })
            .always(function() {
                // After done or fail, we will remove the spinner here
                $('#spinner').removeClass('spinner-border');
            });
        }

        // This function will dynamically add the contents to the container
        function clearRecipes() {
            $(searchItem).empty();
            $("#recipe-rows-container").empty();
        }

        function addToContainer(result) {
            var myContainer = document.querySelector('#recipe-rows-container');

            if (result.meals !== null && result.meals !== '' && result.meals !== undefined) {
                // Loop through the array of objects
                for (var i = 0; i < result.meals.length; i++) {
                    var card = document.createElement('div');
                    card.classList.add('card');

                    // Add Images and append them to the card
                    var img = document.createElement('img');
                    img.classList.add('card-img-top');
                    img.src = result.meals[i].strMealThumb;
                    img.alt = result.meals[i].strMeal + ' recipe';

                    var cardBody = document.createElement('div');
                    cardBody.classList.add('card-body');

                    // Create the title for each recipe name in the card
                    var title = document.createElement('h5');
                    title.classList.add('card-title');
                    title.innerText = result.meals[i].strMeal;

                    // Create the "Recipe" button
                    var recipeButton = document.createElement('button');
                    recipeButton.classList.add('btn', 'btn-primary');
                    recipeButton.innerText = 'Recipe';
                    // Use the meal ID as a unique identifier for the recipe
                    recipeButton.setAttribute('data-meal-id', result.meals[i].idMeal);
                    recipeButton.addEventListener('click', function() {
                        var mealId = this.getAttribute('data-meal-id');
                        showRecipeDetails(mealId);
                    });

                    // Append elements to the card
                    cardBody.appendChild(title);
                    cardBody.appendChild(recipeButton);
                    card.appendChild(img);
                    card.appendChild(cardBody);

                    // Append the card to the recipe-rows-container
                    myContainer.appendChild(card);
                }
            }
        }

        // Function to show recipe details when the "Recipe" button is clicked

function showRecipeDetails(recipeId) {
    // Fetching the recipe details using another AJAX call to the API
    $.ajax('https://www.themealdb.com/api/json/v1/1/lookup.php?i=' + recipeId)
        .done(function(recipeDetails) {
            // Handle the response and display the details
            if (recipeDetails.meals && recipeDetails.meals.length > 0) {
                var selectedRecipe = recipeDetails.meals[0];

                // Extract the recipe title, instructions, category, and area
                var recipeTitle = selectedRecipe.strMeal;
                var recipeInstructions = selectedRecipe.strInstructions;
                var recipeCategory = selectedRecipe.strCategory;
                var recipeArea = selectedRecipe.strArea;

                // Display the details in a modal or a separate section
                displayRecipeDetails(recipeTitle, recipeInstructions, recipeCategory, recipeArea);
            } else {
                alert('Recipe not found');
            }
        })
        .fail(function(xhr, status, error) {
            console.log("Error: " + error);
        });
}

function displayRecipeDetails(title, instructions, category, area) {
    // You can customize this part to display the details as you prefer.
    // For now, it shows the details in an alert.
    var detailsMessage = "Recipe Title: " + title + 
                       "\n\nCategory: " + category + 
                       "\nArea: " + area + 
                       "\n\nInstructions:\n" + instructions;
    alert(detailsMessage);
}  
    </script>

<!-- Include Bootstrap JavaScript -->
 <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"></script>
    
</body>
</html>
