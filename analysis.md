Data & Ai Final Project Report
-	Steam Game Recommendation System

I. Introduction

Objective: The goal of this project was to develop a system that recommends games to users when they want to play a new game, using collaborative filtering through data from users with similar tastes

Motivation: The reason I decided to develop this system stems from my enjoyment of Steam games. Whenever I visited the Steam homepage, I was often intrigued by how it automatically recommended new games in genres I liked. This fascinating experience sparked my interest, and I became motivated to develop a similar recommendation system myself.

Dataset: 
The dataset I used is the Steam 200k dataset, which I downloaded from Kaggle. 
This dataset consists of 5 columns: user id, game name, behavior, play time, and 0.


II. Project Development and Challenges

In the Development Process section, I will first explain the process without any images. Then, in the Challenges and Failures and Experiments and Solutions, I will provide additional explanations with images.

Development Process: 
1. Process the given dataset to make it usable. I wrote code to convert play time into ratings by changing it to values from 1 to 5 based on time. I also deleted columns consisting only of zeros. Additionally, I removed all rows that had 'purchase' in the Behavior column to eliminate unnecessary data.
2. I modified the code from Assignment 3 to fit the processed data, preparing to train the Steam game recommendation model.
3. Load the data and train the model.
4. Calculate the bias values for each item from the trained model.
5. Compute the Cosine Similarity values between items. Here, the Cosine Similarity values are calculated between one game selected by the user and all other games. (Collaborative Filtering)
6. Output the names of the top 5 items (games) and provide them to the user.

Challenges and Failures / Experiments and Solutions:



The first problem occurred while converting play time to ratings. After removing outliers from play time, simply replacing it with values from 0 to 5 caused the following issue

 
1. Excessive imbalance in the data
2. Need to remove data with high play time to avoid affecting other data (because of division by max value)
3. Increased data sparsity due to too many possible rating values
4. Increased noise

Therefore, to solve these problems, I changed the data to use only integer values from 1 to 5. 
Through this data transformation, the data distribution was made more uniform.
Before Transformation		After Transformation.
   


The next problem that occurred was the absence of unique game IDs for each game title.

This made it difficult to handle the data, and during the process of calculating bias, calculations were made based on User Id, resulting in the creation of 20 biases for a single game. Therefore, I created a new CSV file by assigning sequential numbers to each game.
Below is the code: 
 


Next, many bias values were calculated as 0, which caused issues during the process of calculating Cosine Similarity. To address this, I modified the `MatrixFactorizerWithBias` function to randomly assign very small values to the bias, ensuring that no problems occurred due to bias values being 0 

Additionally, I modified the code to add an epsilon value before calculating the cosine similarity, ensuring that zero values would never occur. 


The final Challenge had to address was solving overfitting.
The first approach I tried was modifying the ratio for splitting the data. Unlike the MovieLens dataset, this dataset has a much smaller quantity, so I increased the proportion of the valid_set to use more data for validation. 
However, I tried varying the proportion of the valid_set from 5% to 30%, but no significant changes occurred, so I decided not to use this method.

Next, I tried modifying the Optimizer and Data Loader. 
After reading the PyTorch documentation for torch.optim.Adam, I thought that adjusting the learning_rate and weight_decay could help reduce overfitting. So, I modified the learning_rate and weight_decay parameters and retrained the model.
Furthermore, as the batch_size was originally set for the MovieLens dataset, I experimented with various values to observe different results.

I set the learning_rate value to 2e-3 and the weight_decay value to 1e-4.
Also, since the dataset size is not very large, I reduced the batch_size to 256.
Before					After
  


III. Result and Analysis

Outcomes and Visuals:	
First, here is the Train and Valid Accuracy graph. 
 
Looking at how the accuracy remains at low values and doesn't exceed 0.4, it's difficult to say that the model learned well.

Although the accuracy was low, I still proceeded to calculate bias values and similarities using the trained model.


 
The images above show games with high bias values
The main characteristics of games with high bias values are that they predominantly feature strategy, action, and multiplayer genres. Approximately 80% of these games are in the action genre, and almost all of them involve real-time interaction with other players. 
Additionally, we can observe that games that are quite well-known and have been played by many people, or currently maintain high concurrent user numbers, tend to have higher bias values.
This appears to be related to the fact that we converted play time to ratings for this project. This is because many real-time games, by their nature, require longer playing times. 
The above image shows games with low bias values. 
These games don't share any common genre patterns. 
However, unlike games with high bias values, there are many single-player games. 
Additionally, some games discontinued their service quickly without long-term support, and most games lack DLCs or expansion packs. 
These games are predominantly indie or medium-scale games.

One concerning aspect among the low bias games is that Geometry Dash (#2790) received a low bias value. 
Since Geometry Dash is a globally popular rhythm game and checking the Steam site shows that it has very positive reviews, this made me think that the criterion of "games played by many people" that we discussed when analyzing high bias shouldn't be applied to bias values. 
This issue likely arose during the process of converting play time to ratings.



I wrote code to recommend similar games to user-selected games by calculating cosine similarity using these bias values. 
I will verify if the training was successful through three results. I selected two games that I know and have played, and one game was randomly chosen to check the results.

1. Poly Bridge
 
Poly Bridge & Analogue: A Hate Story
- Both focus on problem-solving gameplay
- Include puzzle elements requiring logical thinking
- User decisions significantly impact game progression
- Relatively slow-paced gameplay

Poly Bridge & Surgeon Simulator
- Physics engine-based gameplay
- Simulation elements requiring precise control
- Learning through trial and error is important
- Includes humorous failure elements

Poly Bridge & DEFCON
- Gameplay requiring strategic thinking
- Importance of resource management
- Need to predict and plan outcomes
- Process of finding optimized solutions

Poly Bridge & ORION Prelude
- Contains construction/destruction elements
- Stage-by-stage progression structure
- Multiple approaches possible for goal achievement
- Supports experimental play styles

Poly Bridge & Gone Home
- Exploration elements present
- Player can control progression pace
- Includes storytelling elements
- Structure allows retrying after failure

2. Grand Theft Auto IV
 
GTA IV & GRID 2
- Both feature vehicle-based gameplay
- Open environments for driving/racing
- Detailed physics engine for vehicle handling
- Urban setting and environment
- Focus on vehicle customization and performance

GTA IV & Offworld Trading Company
- Resource management aspects
- Economic systems play a role
- Strategic decision-making elements
- Multiple ways to achieve objectives
- Complex interconnected systems

GTA IV & Train Simulator
- Vehicle operation mechanics
- Large open world environment
- Realistic physics simulation
- Transportation-focused elements
- Attention to detail in vehicle behavior

GTA IV & Sengoku
- Territory control elements
- Strategic resource management
- Character relationship systems
- Multiple approaches to objectives
- Complex narrative elements

GTA IV & Battle Islands
- Territory control mechanics
- Resource management
- Strategic planning elements
- Progressive difficulty scaling
- Multiple mission types

3. Dead Island
 
Dead Island & Impire
- Combat system involving monsters/enemies
- Character progression and development systems

Dead Island & Shovel Knight
- Action-focused combat system
- Various weapon usage mechanics

Dead Island & Sorcerer King
- Survival elements
- Resource collection and management

Dead Island & MASSIVE CHALICE
- Combat-centered gameplay
- Character management systems

Dead Island & Hell Yeah!
- Action-focused gameplay
- Various weapon systems

While there are overlapping elements between the games, one concerning aspect is that although they share gameplay systems (mechanics), their overall genres and game styles are quite different. 
From the perspective of actual gameplay experience, the feeling users get might not be what they're looking for, making it difficult to say that all these games are well-recommended matches.


IV. Reflection and Learning

Personal Learning and Failure Analysis: 

The first thing I learned is that when using someone else's code, it's crucial to thoroughly understand it and carefully consider whether it's applicable to my chosen dataset.

While it would have been better to have a clear understanding of the code before starting this project, I had to understand it as problems arose, which took more time and likely created issues that could have been avoided with proper prior understanding.
Additionally, I spent about 90% of my time on model training, trying various parameter adjustments, but the more I worked on it, the more I realized how limited my dataset was. 
This reinforces the importance of thoroughly understanding others' code and considering its applicability to your dataset before implementation.
Furthermore, it's crucial to use data appropriate for the subject matter. I've come to believe that data quality is the most important aspect of machine learning and deep learning.
Particularly, while converting play time values to ratings arbitrarily, there were many instances where I thought, "This high value probably isn't due to actual user preference..." This experience made me realize the importance of putting more effort into finding the right dataset before starting a project.

I consider this project to be a failure, and I believe that the most crucial factor in preventing such failure would be selecting a dataset that is appropriate for the subject topic.
