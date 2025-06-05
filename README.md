<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>&#129369 Recipe Ratings and Simplicity Analysis &#129368</title>
  <style>
    body {
      font-family: Roboto, sans-serif;
      line-height: 1.6;
      margin: 2rem;
      background-color: #f9f9f9;
      color: #fffdfd;
    }
    h1, h2, h3 {
      color:rgb(71, 124, 177);
    }
    code {
      background:rgba(99, 99, 99, 0.45);
      padding: 2px 4px;
      border-radius: 4px;
    }
    .section {
      margin-bottom: 3rem;
    }
    table {
      background-color:rgba(255, 255, 255, 0.95);
      color:rgb(0, 0, 0);
      padding: 1rem;
      border: 1px solid #ccc;
      margin: 1rem 0;
    }
    tr:hover {
      background-color:rgb(199, 199, 199);
    }
    th {
      color: black !important;
    }
    .row_heading {
      color: black !important;
    }
    /* Fixed sidebar */
    .sidebar {
    position: fixed;
    top: 0;
    left: 0;
    width: 200px;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.6); /* translucent black */
    padding-top: 20px;
    border-right: 1px solid #ccc;
    }
    .sidebar a {
    display: block;
    padding: 10px 15px;
    text-decoration: none;
    color: white;       /* white text */
    font-weight: bold;  /* bold text */
    }
    .sidebar a:hover {
    background-color: rgba(255, 255, 255, 0.2);
    }
    .content {
      margin-left: 220px; /* space for sidebar */
      padding: 20px;
    }
  </style>
</head>
<body>
  
  <div class="sidebar">
    <a href="#introduction">Introduction</a>
    <a href="#data-cleaning">Data Cleaning</a>
    <a href="#univariate-analysis">Univariate Analysis</a>
    <a href="#bivariate-analysis">Bivariate Analysis</a>
    <a href="#missingness">Assessment of Missingness</a>
    <a href="#hypothesis-testing">Hypothesis Testing</a>
    <a href="#prediction">Prediction</a>
    <a href="#baseline-model">Baseline Model</a>
    <a href="#final-model">Final Model</a>
    <a href="#fairness">Fairness</a>
  </div>
  <h1>Recipe Ratings and Simplicity Analysis</h1>

  <div class="section" id="introduction">
    <h2>Introduction</h2>
    <p>Our dataset contains information on recipes including variables of interest such as minutes for each recipe, the number of reviews for each unique recipe, ratings (scored from 1-5), average ratings, and the number of steps to complete the recipe. We aim to investigate people’s preferences for different recipes using ratings as a proxy for preference, and how these preferences relate to the complexity of a recipe.</p>

    <p>We define complexity using the number of steps, time, and number of ingredients, with lower being easier and higher being more difficult. Our goal is to understand if people tend to prefer simpler (easier) recipes and whether these receive higher average ratings. This analysis may help identify high-quality, easy-to-make recipes that maximize satisfaction while minimizing effort.</p>

    <p>The dataset includes <strong>234,429</strong> rows (individual reviews of recipes) and 17 columns. We focus on 7 of these columns:</p>
    <ul>
      <li><code>id</code> (int): unique recipe ID</li>
      <li><code>minutes</code> (int): total time in minutes</li>
      <li><code>n_steps</code> (int): number of steps to complete</li>
      <li><code>n_ingredients</code> (int): number of ingredients</li>
      <li><code>rating</code> (int): rating given in a review</li>
      <li><code>average_rating</code> (float): average rating per recipe</li>
    </ul>
  </div>

  <div class="section" id="data-cleaning">
    <h2>Data Cleaning and Exploratory Data Analysis</h2>
    <p>We merged the <code>recipes</code> and <code>ratings</code> datasets on the <code>id</code> column (left join). This allowed us to see the ratings of each recipe. We changed ratings of 0 to <code>np.nan</code> in the rating column so it does not skew the average rating for each recipe. We computed <code>average_rating</code> by grouping by recipe ID and averaging all corresponding ratings, then merged this back into our main DataFrame. This helped to see the average rating among all reviews for a specific recipe. Then, we converted <code>rating</code> column to <code>Int8</code> for efficiency.</p>

  <p>Preview of Data:</p>
  <table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right; color: black">
      <th></th>
      <th>id</th>
      <th>minutes</th>
      <th>n_steps</th>
      <th>n_ingredients</th>
      <th>rating</th>
      <th>average rating</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>333281</td>
      <td>40</td>
      <td>10</td>
      <td>9</td>
      <td>4</td>
      <td>4.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>453467</td>
      <td>45</td>
      <td>12</td>
      <td>11</td>
      <td>5</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>306168</td>
      <td>40</td>
      <td>6</td>
      <td>9</td>
      <td>5</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>306168</td>
      <td>40</td>
      <td>6</td>
      <td>9</td>
      <td>5</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>306168</td>
      <td>40</td>
      <td>6</td>
      <td>9</td>
      <td>5</td>
      <td>5.0</td>
    </tr>
  </tbody>
  </table>
  <p>Duplicate columns are not dropped, because the <code>rating</code> column still records individual ratings per review, which will be used in later analyis.</p>
  </div>

  <div class="section" id="univariate-analysis">
    <h2>Univariate Analysis</h2>
    <h3>Distribution of Minutes</h3>
    <p>The distribution is heavily concentrated on lower values with the mode being much less than the mean. It seems most recipes take less time than the mean, while some outliers taking much more time.</p>
    <div class="plot">
    <iframe 
    src="plot/mins_distribution.html"
    width="100%"
    height="600"
    frameborder="0"
    ></iframe>
    </div>
    <h3>Distribution of the Number of Ingredients</h3>
    <p>The number of ingredients seem to have a bell-shaped curve. The number of ingredients seem to be normally distributed.</p>
    <div class="plot">
    <iframe 
    src="plot/ing_distribution.html"
    width="100%"
    height="600"
    frameborder="0"
    ></iframe>
    </div>
    <h3>Number of Steps Distribution</h3>
    <p>The number of steps is approximately normally distributed with a mean of 10.02 and a right-skewed tail.</p>
    <div class="plot">
    <iframe 
    src="plot/steps_distribution.html"
    width="100%"
    height="600"
    frameborder="0"
    ></iframe>
    </div>
  </div>

  <div class="section" id="bivariate-analysis">
    <h2>Bivariate Analysis</h2>
    <h3>Number of Ingredients vs. Rating</h3>
    <p>This plot shows a slight positive trend: recipes with more ingredients tend to receive higher average ratings.</p>
    <div class="plot">
    <iframe 
    src="plot/ing_rating.html"
    width="100%"
    height="600"
    frameborder="0"
    ></iframe>
    </div>
    <h3>Minutes vs. Rating</h3>
    <p>There does not seem to be a correlation between minutes and rating.</p>
    <div class="plot">
    <iframe 
    src="plot/min_rating.html"
    width="100%"
    height="600"
    frameborder="0"
    ></iframe>
    </div>
    <h3>Number of Steps vs. Rating</h3>
    <p>This plot shows a slight positive trend: recipes with more steps tend to receive higher average ratings.</p>
    <div class="plot">
    <iframe 
    src="plot/steps_rating.html"
    width="100%"
    height="600"
    frameborder="0"
    ></iframe>
    </div>
    <h3>Interesting Aggregates</h3>
    <h4>Pivot Table: Number of Steps vs. Ratings</h4>
    <p>Ratings do not consistently rise or fall with number of steps, suggesting no strong effect.</p>
    <div class="table">
    <table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>average rating</th>
      <th>rating</th>
    </tr>
    <tr>
      <th>steps_bin</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>(0.901, 20.8]</th>
      <td>4.676063</td>
      <td>4.679352</td>
    </tr>
    <tr>
      <th>(20.8, 40.6]</th>
      <td>4.682909</td>
      <td>4.687489</td>
    </tr>
    <tr>
      <th>(40.6, 60.4]</th>
      <td>4.683892</td>
      <td>4.68682</td>
    </tr>
    <tr>
      <th>(60.4, 80.2]</th>
      <td>4.825431</td>
      <td>4.818182</td>
    </tr>
    <tr>
      <th>(80.2, 100.0]</th>
      <td>4.727273</td>
      <td>4.72</td>
    </tr>
  </tbody>
</table>
    </div>
    <h4>Pivot Table: Number of Ingredients vs. Ratings</h4>
    <p>Similarly, ingredient count shows no consistent correlation with rating.</p>
    <div class="table">
    <table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>average rating</th>
      <th>rating</th>
    </tr>
    <tr>
      <th>ingredients_bin</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>(0.964, 3.4]</th>
      <td>4.724693</td>
      <td>4.728573</td>
    </tr>
    <tr>
      <th>(3.4, 5.8]</th>
      <td>4.701702</td>
      <td>4.706692</td>
    </tr>
    <tr>
      <th>(5.8, 8.2]</th>
      <td>4.665432</td>
      <td>4.669343</td>
    </tr>
    <tr>
      <th>(8.2, 10.6]</th>
      <td>4.660374</td>
      <td>4.663089</td>
    </tr>
    <tr>
      <th>(10.6, 13.0]</th>
      <td>4.679133</td>
      <td>4.682083</td>
    </tr>
    <tr>
      <th>(13.0, 15.4]</th>
      <td>4.666557</td>
      <td>4.6671</td>
    </tr>
    <tr>
      <th>(15.4, 17.8]</th>
      <td>4.672241</td>
      <td>4.672691</td>
    </tr>
    <tr>
      <th>(17.8, 20.2]</th>
      <td>4.702077</td>
      <td>4.707473</td>
    </tr>
    <tr>
      <th>(20.2, 22.6]</th>
      <td>4.765671</td>
      <td>4.772025</td>
    </tr>
    <tr>
      <th>(22.6, 25.0]</th>
      <td>4.767451</td>
      <td>4.770161</td>
    </tr>
    <tr>
      <th>(25.0, 27.4]</th>
      <td>4.772905</td>
      <td>4.781955</td>
    </tr>
    <tr>
      <th>(27.4, 29.8]</th>
      <td>4.877691</td>
      <td>4.878788</td>
    </tr>
    <tr>
      <th>(29.8, 32.2]</th>
      <td>4.857143</td>
      <td>4.857143</td>
    </tr>
    <tr>
      <th>(32.2, 34.6]</th>
      <td>5.000000</td>
      <td>5.0</td>
    </tr>
    <tr>
      <th>(34.6, 37.0]</th>
      <td>5.000000</td>
      <td>5.0</td>
    </tr>
  </tbody>
</table>
</div>
  </div>

  <div class="section" id="missingness">
    <h2>Assessment of Missingness</h2>
    <p>Columns <code>rating</code>, <code>review</code>, and <code>description</code> have missing values.</p>
    <ul>
      <li><strong>Rating:</strong> Likely NMAR – users who disliked a recipe might be less inclined to leave a rating.</li>
      <li><strong>Review:</strong> Also likely NMAR – may correlate with user satisfaction or emotional investment.</li>
      <li><strong>Description:</strong> Likely MAR – possibly missing more often in simple recipes.</li>
    </ul>
    <h3>Missingness Dependency</h3>
    <h4>Permutation Test: Rating vs. Review Missingness</h4>
    <p>No significant difference – review missingness is likely not dependent on rating.</p>
    <div class="plot">
    <iframe 
    src="plot/rating_missing.html"
    width="100%"
    height="600"
    frameborder="0"
    ></iframe>
    </div>
    <h4>Permutation Test: Number of Steps vs. Review Missingness</h4>
    <p>Significant difference – recipes with reviews have fewer steps. Suggests MAR based on complexity.</p>
    <div class="plot">
    <iframe 
    src="plot/n_steps_missing.html"
    width="100%"
    height="600"
    frameborder="0"
    ></iframe>
    </div>
  </div>

  <div class="section" id="hypothesis-testing">
    <h2>Hypothesis Testing</h2>
    <p><strong>Question:</strong> Do simpler recipes (fewer ingredients) get higher ratings?</p>

    <h3>Hypotheses</h3>
    <ul>
      <li>H₀: No difference in average ratings between simple and complex recipes</li>
      <li>H₁: Simple recipes have higher average ratings</li>
    </ul>

    <h3>Test Setup</h3>
    <ul>
      <li>Simple = fewer ingredients than dataset median</li>
      <li>Test statistic = difference in mean ratings</li>
      <li>Permutation test (1,000 iterations)</li>
      <li>One-sided test</li>
    </ul>

    <h3>Results</h3>
    <ul>
      <li>Observed difference: 0.0072</li>
      <li>p-value: 0.0000</li>
    </ul>

    <p><strong>Conclusion:</strong> Reject H₀. Simpler recipes have statistically higher average ratings, although effect size is small.</p>
  </div>

  <div class="section" id="prediction">
    <h2>Framing a Prediction Problem</h2>

    <h3>Prediction Type</h3>
    <p>Regression problem — predicting numerical <code>rating</code>.</p>

    <h3>Target Variable</h3>
    <p><code>rating</code>: numerical score (1–5) given by users.</p>

    <h3>Evaluation Metric</h3>
    <p>Root Mean Squared Error (RMSE) — penalizes large errors, interpretable in same units.</p>

    <h3>Features Used (No Leakage)</h3>
    <ul>
      <li><code>minutes</code></li>
      <li><code>n_steps</code></li>
      <li><code>n_ingredients</code></li>
    </ul>
  </div>

  <div class="section" id="baseline-model">
    <h2>Baseline Model</h2>
    <p>Model used: Linear Regression with 3 quantitative features. All features were already numeric, no encoding required.</p>
    <p>Performance: RMSE = [insert value here]</p>
    <p>Model is a reasonable baseline; interpretability and simplicity are advantages, but it may underfit.</p>
  </div>

  <div class="section" id="final-model">
    <h2>Final Model</h2>
    <p>Final model used: [e.g. Random Forest, Gradient Boosting]</p>
    <p>New features added: [e.g. interaction terms, polynomial transformations]</p>
    <p>Rationale: These features better capture non-linear effects and interactions.</p>
    <p>Hyperparameter tuning via [e.g. GridSearchCV].</p>
    <p>Improved RMSE: [insert new value]</p>

    <div class="plot"><p><em>Insert model performance plot here</em></p></div>
  </div>

  <div class="section" id="fairness">
    <h2>Fairness Analysis</h2>
    <p><strong>Group X:</strong> Recipes with fewer than median ingredients</p>
    <p><strong>Group Y:</strong> Recipes with greater than or equal to median ingredients</p>
    <p><strong>Metric:</strong> Mean predicted rating</p>
    <p><strong>Hypotheses:</strong></p>
    <ul>
      <li>H₀: No difference in prediction accuracy or error across groups</li>
      <li>H₁: Prediction error is systematically different between groups</li>
    </ul>
    <p>Test statistic: Difference in RMSE between groups</p>
    <p>p-value: [insert value]</p>
    <p><strong>Conclusion:</strong> [insert conclusion about fairness]</p>

    <div class="plot"><p><em>Insert fairness visualization here</em></p></div>
  </div>

</body>
</html>
