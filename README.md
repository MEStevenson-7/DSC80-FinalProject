<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Exploring Recipe Ratings</title>
  <style>
    body {
      font-family: 'Roboto', sans-serif;
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
      width: 95%;
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
    .left-sidebar {
      position: fixed;
      top: 0;
      left: 0;
      width: 200px;
      height: 100%;
      background: #1e1e1e;
      color: white;
      text-align: center;
      padding-top: 20px;}
    .right-sidebar {
      position: fixed;
      top: 0;
      right: 0;
      width: 250px;
      height: 100%;
      color: white;
      text-align: center;
      padding-top: 20px;
    }
    .left-sidebar a {
    display: block;
    padding: 10px 15px;
    text-decoration: none;
    color: white;       /* white text */
    font-weight: bold;  /* bold text */
    }
    .left-sidebar a:hover {
      transform: scale(1.1);
      box-shadow: 0 0 5px white;
    }
    .right-sidebar img {
      width: 100%;
      height: 100px;
      margin: 0px 0;
      border-radius: 5px;
      transition: transform 0.2s;
      cursor: pointer;
    }
    .right-sidebar img:hover {
      transform: scale(1.1);
      box-shadow: 0 0 5px white;
    }
    .content {
      margin-left: 220px; 
      padding: 20px;
    }
    figure {
      text-align: center;
    }
    figcaption {
      font-size: 12px;
      color:rgb(52, 52, 52);
      margin-top: 0px;
      background-color:rgb(108, 194, 255);
      border-radius: 15px;
    }
    figcaption:hover {
      transform: scale(1.1);
      box-shadow: 0 0 5px white;
    }
  </style>
</head>
<body>
  <div class="left-sidebar">
    <h4 style="margin-top: 0; text-align: center; color: #ffffff;">Directory</h4>
    <a href="#top">🏠 Back to Top</a>
    <a href="#introduction">&#128075; Introduction</a>
    <a href="#data-cleaning">&#129529; Data Cleaning</a>
    <a href="#univariate-analysis">&#128201; Univariate Analysis</a>
    <a href="#bivariate-analysis">&#128200; Bivariate Analysis</a>
    <a href="#missingness">&#128269; Assessment of Missingness</a>
    <a href="#hypothesis-testing">&#129514; Hypothesis Testing</a>
    <a href="#prediction">&#128302; Prediction</a>
    <a href="#baseline-model">&#128218; Baseline Model</a>
    <a href="#final-model">&#128202; Final Model</a>
    <a href="#fairness">🤝 Fairness</a>
  </div>
  <div id="top"></div>
  <h1>&#129369; Recipe Ratings and Simplicity Analysis &#129368;</h1>

  <div class="right-sidebar">
    <h4 style="margin-top: 0; text-align: center; color: #ffffff;">Our Favorite Recipes</h4>
    <a href="https://www.food.com/recipe/buttermilk-pancakes-66241" style="text-decoration: none;">
    <figure>
      <img src="https://img.sndimg.com/food/image/upload/f_auto,c_thumb,q_55,w_960/v1/img/recipes/66/24/1/CiPfUQpLROOV2Bedo79z_buttermilk%2520pancakes%252066241-7.jpg">
      <figcaption>Buttermilk Pancakes</figcaption>
    </figure>
    </a>
    <a href="https://www.food.com/recipe/onigiri-japanese-rice-balls-8624" style="text-decoration: none;">
    <figure>
      <img src="https://img.sndimg.com/food/image/upload/f_auto,c_thumb,q_55,w_iw/v1/img/recipes/86/24/F5ccjMkFReC48UQ4jfew_0S9A8582.jpg">
      <figcaption>Onigiri</figcaption>
    </figure>
    </a>
    <a href="https://www.food.com/recipe/creamy-cajun-chicken-pasta-39087#recipe" style="text-decoration: none;">
    <figure>
      <img src="https://img.sndimg.com/food/image/upload/f_auto,c_thumb,q_55,w_iw/v1/img/recipes/39/08/7/VPeSMiYHRce4BWsyj7Nl_0S9A5582.jpg">
      <figcaption>Creamy Cajun Chicken Pasta</figcaption>
    </figure>
    </a>
    <a href="https://www.food.com/recipe/sangria-shortcakes-538531" style="text-decoration: none;">
    <figure>
      <img src="https://img.sndimg.com/food/image/upload/f_auto,c_thumb,q_55,w_960/v1/img/recipes/53/85/31/wNert6lMRSaCPeoyrCav_0S9A0215.jpg">
      <figcaption>Sangria Shortcakes</figcaption>
    </figure>
    </a>
  </div>

  <div class="section" id="introduction">
    <h2>&#128075; Introduction</h2>
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
    <h2>&#129529; Data Cleaning and Exploratory Data Analysis</h2>
    <p>We merged the <code>recipes</code> and <code>ratings</code> datasets on the <code>id</code> column (left join). This allowed us to see the ratings of each recipe. We changed ratings of 0 to <code>np.nan</code> in the rating column so it does not skew the average rating for each recipe. We computed <code>average_rating</code> by grouping by recipe ID and averaging all corresponding ratings, then merged this back into our main DataFrame. This helped to see the average rating among all reviews for a specific recipe. Then, we converted <code>rating</code> column to <code>Int8</code> for efficiency.</p>

  <p>Preview of Data:</p>
  <div class="table">
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
  </div>
  <p>Duplicate columns are not dropped, because the <code>rating</code> column still records individual ratings per review, which will be used in later analyis.</p>
  </div>

  <div class="section" id="univariate-analysis">
    <h2>&#128201; Univariate Analysis</h2>
    <h3>Distribution of Minutes</h3>
    <p>The distribution is heavily concentrated on lower values with the mode being much less than the mean. It seems most recipes take less time than the mean, while some outliers skew the mean to the right.</p>
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
    <h2>&#128200; Bivariate Analysis</h2>
    <h3>Number of Ingredients vs. Rating</h3>
    <p>This plot shows a slight positive trend: recipes with more ingredients tend to receive higher average ratings. Although we used a linear trend to describe the correlation, a polynomial trend would fit the data better for predictions.</p>
    <div class="plot">
    <iframe 
    src="plot/ing_rating.html"
    width="100%"
    height="600"
    frameborder="0"
    ></iframe>
    </div>
    <h3>Minutes vs. Rating</h3>
    <p>There does not seem to be a correlation between minutes and rating. </p>
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
    <p>Similarly, ingredient count shows no consistent correlation with rating. However, there seems to be a quadratic relation with the minimum rating occuring between 8 to 10 ingredients.</p>
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
    <h2>&#128269; Assessment of Missingness</h2>
    <p>Columns <code>rating</code>, <code>review</code>, and <code>description</code> have missing values.</p>
    <ul>
      <li><strong>Rating:</strong> Likely NMAR – users who disliked a recipe might be less inclined to leave a rating. If there is a column where users input yes or no to the question "did you like this recipe," it could make this MAR. The missing rating could be because the user did not like the recipe and chose not to rate.</li>
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
    <h2>&#129514; Hypothesis Testing</h2>
    <p><strong>Question:</strong> Do simpler recipes (fewer ingredients) get higher ratings?</p>
    <h3>Hypotheses</h3>
    <ul>
      <li>H₀: No difference in average ratings between simple and complex recipes</li>
      <li>H₁: Simple recipes have higher average ratings</li>
    </ul>
    <h3>Test Setup</h3>
    <ul>
      <li>Simple = fewer ingredients than dataset median</li>
      <li>Test statistic = difference in mean ratings. This would be the best test statistic to use since we are observing difference in means.</li>
      <li>Significance level = 0.05. We want a 95% confidence level for the null hypothesis.</li>
      <li>Permutation test (1,000 iterations). We used 1000 iterations for the permutation test because it provides a reliable estimate of the p-value with acceptable variability.</li>
      <li>One-sided test</li>
    </ul>
    <h3>Results</h3>
    <div class="plot">
    <iframe 
    src="plot/permutation_test.html"
    width="100%"
    height="600"
    frameborder="0"
    ></iframe>
    </div>
    <ul>
      <li>Observed difference: 0.0086</li>
      <li>p-value: 0.0000</li>
    </ul>
    <p><strong>Conclusion:</strong> Reject H₀. Simpler recipes seem to have statistically higher average ratings, although effect size is small.</p>
  </div>

  <div class="section" id="prediction">
    <h2>&#128302; Framing a Prediction Problem</h2>
    <p>We aim to predict the individual rating a user might give a recipe based on various observable features of that recipe. Understanding what factors contribute to higher or lower ratings can help us better assess recipe quality and user preferences, even before a recipe receives many reviews.</p>
    <h3>Prediction Type</h3>
    <p>Regression problem — predicting numerical <code>rating</code>.</p>
    <h3>Target Variable</h3>
    <p><code>rating</code>: numerical score (1–5) given by users.</p>
    <p>We chose rating because it is a direct, quantifiable measure of user satisfaction, and predicting it allows us to understand what makes a recipe more or less appealing to users. This connects well with our central theme of exploring how recipe characteristics (such as simplicity) relate to user preferences.</p>
    <h3>Evaluation Metric</h3>
    <p>Root Mean Squared Error (RMSE) — penalizes large errors, interpretable in same units.</p>
    <p>To evaluate our models, we used the R², which measures the proportion of variance in the target variable that is explained by the model’s features. It ranges from 0 to 1 (or negative if the model is worse than a baseline).</p>
    <p>We chose R² over other metrics such as Mean Squared Error (MSE) or Mean Absolute Error (MAE) for model comparison because:
    <ul>
	    <li>R² is scale-independent, making it easier to interpret across different versions of the model.</li>
		  <li>It provides an intuitive understanding of how much of the variation in rating is explained by the features.</li>
	    <li>Unlike MAE or MSE, which report error magnitudes, R² helps determine relative model performance — higher R² means better explanatory power.</li>
	    <li>R² is commonly used for comparing regression models on the same target variable, which was central to our goal of choosing the best model from several candidates.</li>
    </ul>
    We still reported MAE and MSE during evaluation to supplement our understanding of model error, but R² served as the primary metric for determining which model was most effective.</p>
    <p>We used RMSE over metrics like Mean Absolute Error (MAE) or R² because RMSE is particularly helpful when we want to emphasize minimizing large individual prediction errors, which could reflect particularly poor model performance on certain types of recipes.</p>
    <h3>Features Used (No Leakage)</h3>
    <ul>
      <li><code>minutes</code></li>
      <li><code>n_steps</code></li>
      <li><code>n_ingredients</code></li>
    </ul>
  </div>

  <div class="section" id="baseline-model">
    <h2>&#128218; Baseline Model</h2>
    <p>Model used: Linear Regression with 3 quantitative features: number of ingredients, number of steps, and minutes. All features were already numeric, no encoding required.</p>
    <div class='table'>
    <table border="1" class="dataframe">
      <thead>
        <tr style="text-align: right;">
          <th>Feature Name</th>
          <th>Type</th>
          <th>Description</th>
          <th>Transformation</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>n_steps</td>
          <td>Ordinal</td>
          <td>number of steps in the recipe</td>
          <td>binarized into "few" (≤5) vs. "many" (>5)</td>
        </tr>
        <tr>
          <td>minutes</td>
          <td>Ordinal</td>
          <td>total time required to complete the recipe in minutes</td>
          <td>binarized into "short" (≤30) vs. "long" (>30)</td>
        </tr>
        <tr>
          <td>n_ingredients</td>
          <td>Quantitative</td>
          <td>number of ingredients in the recipe</td>
          <td>no transformation</td>
        </tr>
      </tbody>
    </table>
    </div>
    <p>Performance:</p>
    <div class='table'>
    <table border="1" class="dataframe">
      <thead>
        <tr style="text-align: right;">
          <th>Metric</th>
          <th>Value</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <th>Mean Absolute Error (MAE)</th>
          <td>0.4960</td>
        </tr>
        <tr>
          <th>Root Mean Squared Error (RMSE)</th>
          <td>0.7136</td>
        </tr>
        <tr>
          <th>R²</th>
          <td>0.0008</td>
        </tr>
      </tbody>
    </table>
    </div>
    <p>Interpretation:
    <ul>
      <li>The R² Score of 0.0008 suggests that the model explains less than 1% of the variance in recipe ratings, indicating very weak predictive power.</li>
      <li>While the MAE of ~0.5 suggests that on average the model’s predictions are within half a star of the actual rating, this is only slightly better than always predicting the mean rating.</li>
      <li>These results suggest that our current model is not particularly good.</li>
    </ul>
    </p>
    <p>Model is a reasonable baseline; interpretability and simplicity are advantages, but it may underfit.</p>
  </div>

  <div class="section" id="final-model">
    <h2>&#128202; Final Model</h2>
    <p>Final model used: RandomForest</p>
    <p>New features added: 
      <ul>
        <li><strong>Standard scaled minutes:</strong> We applied StandardScaler to normalize the minutes column. This transformation centers the data to have mean 0 and variance 1, which can help many models (especially tree-based ones) distinguish extreme values better.</li>
        <li><strong>Linearization of n_ingredients:</strong> We cubed n_ingredients. During our bivariate analysis, we mentioned that a polynomial transformation would fit the data better. We will implement that transformation here by cubing n_ingredients.</li>
      </ul>
    </p>
    <p>These choices are informed by the data-generating process:
      <ul>
        <li>The number of ingredients and prep time (minutes) are not uniformly distributed, which makes them harder for models to interpret directly.</li>
        <li>These features plausibly affect recipe rating — longer or overly complex recipes may deter high ratings, and ingredients influence complexity as well.</li>
      </ul>
    </p>
    <div class="table">
    <table border="1" class="dataframe">
      <thead>
        <tr style="text-align: right;">
          <th></th>
          <th>Model</th>
          <th>Best Params</th>
          <th>CV R²</th>
          <th>Test R²</th>
          <th>Test MAE</th>
          <th>Test RMSE</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <th>2</th>
          <td>RandomForest</td>
          <td>{'regressor__max_depth': 5, 'regressor__n_estimators': 25}</td>
          <td>0.001523</td>
          <td>0.001463</td>
          <td>0.495351</td>
          <td>0.713336</td>
        </tr>
        <tr>
          <th>1</th>
          <td>DecisionTree</td>
          <td>{'regressor__max_depth': 5, 'regressor__min_samples_split': 2}</td>
          <td>0.001361</td>
          <td>0.001349</td>
          <td>0.495357</td>
          <td>0.713377</td>
        </tr>
        <tr>
          <th>0</th>
          <td>LinearRegression</td>
          <td>{}</td>
          <td>NaN</td>
          <td>0.001071</td>
          <td>0.495879</td>
          <td>0.713476</td>
        </tr>
      </tbody>
    </table>
    </div>
    <p>Hyperparameter tuning via:
      <ul>
        <li><strong>RandomForest:</strong>
        <ul>
          <li>max depth: 5</li>
          <li>number of estimators: 25</li>
        </ul>
        </li>
        <li><strong>DecisionTree:</strong>
        <ul>
          <li>max depth: 5</li>
          <li>minimum samples split: 2</li>
        </ul>
        </li>
        <li><strong>LinearRegression:</strong> none</li> 
      </ul>
      RandomForest performed the best out of the three modeling algorithms.
    </p>
    <p>Improved RMSE: 0.7133</p>
    <p>Improved R²: 0.0015</p>
    <p>The two metrics we observed both had a slight improvement between the baseline model and the final model.</p>
  </div>

  <div class="section" id="fairness">
    <h2>🤝 Fairness Analysis</h2>
    <p><strong>Group X:</strong> Recipes with fewer than median ingredients.</p>
    <p><strong>Group Y:</strong> Recipes with greater than or equal to median ingredients.</p>
    <p><strong>Metric:</strong> Mean predicted rating</p>
    <p><strong>Hypotheses:</strong></p>
    <ul>
      <li>H₀: No difference in prediction accuracy or error across groups.</li>
      <li>H₁: Prediction error is systematically different between groups.</li>
    </ul>
    <p><strong>Test statistic:</strong> Difference in RMSE between groups.</p>
    <p><strong>Significance level:</strong> 0.05</p>
    <p><strong>Conclusion:</strong></p>
    <div class="plot">
    <iframe 
    src="plot/fairness.html"
    width="100%"
    height="600"
    frameborder="0"
    ></iframe>
    </div>
    <p><strong>Actual RMSE difference:</strong> 0.0664</p>
    <p><strong>p-value:</strong> 0.0000</p>
    We reject the null in favor of the alternative hypothesis. It seems there may be a difference in prediction error between groups.
  </div>

</body>
</html>
