# Predicting Red Zone Player Outcomes

By: [Ty Sayball-Wimmer](https://www.linkedin.com/in/ty-sayball-wimmer/)

Undergraduate Track

> "*A guy like Darrelle Revis has been in the NFL a long time. They study tendencies. They know what you're doing from the way you line up... You can't slip.*" - DeAndre Hopkins

## Introduction

When it comes to winning games in the NFL, the **red zone** is the **most critical** spot on the field. Within these 20 yards to the endzone, it is crucial for teams to call the correct play and execute timing perfectly. Those who dominate in the red zone, tend to dominate the game. For defenses, slowing or stopping those powerful red zone offenses can be a challenge. However, analyzing and recognizing offensive tendencies can help give them an upper hand.

This project investigates the pre-snap visuals that a defense has during each play and aims to use those visual queues to assist in predicting which offensive player is most likely to have either a pass reception or a rush attempt. The predicted value is whether or not a player had an outcome on the play by looking for the result of either a rush or pass reception, and predictions with this model update every tenth of a second.

## Methods  

I. FEATURE SELECTION

Features that were selected for this modeling and predicting were determined by taking those that defenses can see visually before the play is run.

Variables deemed as defensive visuals:
- down
- yarsdToGo
- absoluteYardlineNumber
- inMotionAtBallSnap
- shiftSinceLineset
- motionSinceLineset
- x
- y
- o
- position_encoded
- offenseFormation_encoded
- pff_manZone_encoded
- club_encoded


II. FEATURE ENGINEERING

The calculations of 7 new variables were made for this analysis:
- **distanceToNearestDefender1** and **distanceToNearestDefender2** - Euclidean distances that show how close the two nearest defenders are to each player during every frame.
- **distanceToFootball** - Also euclidean distances that show each player's distance from the football in every frame.
- **qbFacingReceiver** - using the orientation variable to see if a player falls within a 45 degree field of view of the quarterback.
- **timeFromHuddleToLineSet** and **timeFromLineSetToSnap** - calculated using distances between events in the 'event' variable.
- **hadOutcome** - determined by whether or not a player had a rushing attempt or a pass reception on each play.

## Modeling

For this analysis, it was determined that an XGBoost model with DMatrix was the optimal choice. This is due to its sequential nature and ability to handle high complexity ML tasks. Also, it is able to handle large datasets easier. Using this, I wanted to make sure that this predictor wouldn't predict backwards, using the end of the play to predict earlier frames. Only frames up until the snap occuring are given to the model, to reduce any chance of the model using after snap events toward its predictions.

<center><img src="https://raw.github.com/tysw01/NFL-Big-Data-Bowl-2025/main/IMAGES/jackson_prediction.png" width="800"/></center>
<center>Figure 1. Run Play Prediction with XGBoost Model.</center>
<br>

<center><img src="https://raw.github.com/tysw01/NFL-Big-Data-Bowl-2025/main/IMAGES/njoku_prediction.png" width="800"/></center>
<center>Figure 2. Pass Play Prediction with XGBoost Model.</center>
<br>

The two above figures are the result of the model running. As can be seen, the model updates its predictions after each frame and it shows each skill player's chances of getting the ball every tenth of a second until the ball is snapped. The first figure is a QB sneak by Lamar Jackson, and the second image is a 4 yard pass completed to David Njoku. The model has an easier time predicting running plays as there generally is only one player who will receive the ball on a run play, as opposed to a pass play.

<center><img src="https://raw.github.com/tysw01/NFL-Big-Data-Bowl-2025/main/IMAGES/roc_curve.png" width="800"/></center>
<center>Figure 3. ROC Curve with AUC for the model's predicting performance.</center>
<br>

The model runs with an accuracy of 91.29% when predicting who is most likely to receive the football. General accuracy score wasn't calculated correctly so I decided that for each play, whoever is predicted in the most frames before the snap, that player was deemed the model's prediction for most likely to have an outcome.

## Results

<center><img src="https://raw.github.com/tysw01/NFL-Big-Data-Bowl-2025/main/IMAGES/shap_analysis.png" width="800"/></center>
<center>Figure 4. SHAP Analysis depicting feature influence.</center>
<br>

A SHAP analysis shows variable importance on the model, and how its values affect the outcomes. One notable point to make with the SHAP analysis is that the values for timeFromHuddleToLineSet only negatively affect a prediction of a player getting the football if there's a high amount of time from huddle break to the ball being snapped. If there's little time from the huddle to the snap, a player is more likely to be predicted. This means that if there's a short amount of time from the huddle break to ball snap, there's less time for the outcome to change. Therefore it can be easier for defenses to determine who can get the ball if teams are quick to start their plays.

<center><img src="https://raw.github.com/tysw01/NFL-Big-Data-Bowl-2025/main/IMAGES/actual_vs_predicted.png" width="800"/></center>
<center>Figure 5. Actual vs. Predicted Outcomes show with player positions</center>
<br>

<center><img src="https://raw.github.com/tysw01/NFL-Big-Data-Bowl-2025/main/IMAGES/HighlightMap1.png"/></center>
<br>
<center><img src="https://raw.github.com/tysw01/NFL-Big-Data-Bowl-2025/main/IMAGES/HighlightMap2.png"/></center>
<center>Figure 6. Highlight tables for the team's percentage of outcomes by position and formation. One table for each skill position.</center>
<br>

It's natural for defenses to expect teams to run the football in the red zone, especially the closer they get to the end zone. But which offense are they playing against? In terms of formations, it'd be more difficult for defenses to use visuals to determine which player to cover if the offense is lined up in empty formation because then it's almost certainly a pass play and the QB has so many options. But with teams lined up in I-formation or with a JUMBO package, it's most certain that teams can single out the RB to get the football (In unknown formations, those are mostly QB sneak plays, unless you're the 2022 Philadelphia Eagles).

Logically, it can be seen that the model is properly predicting by team for which position is most likely to get the football. In wildcat formation, the New Orleans Saints used Taysom Hill in the red zone most of the time, even while listed as a QB. Essentially, you can see what formations each team runs the most, and which position the model predicts to have the football. So, using things like offensive formation, how far players line up from the football, what offense is in the red zone, and timing between motions and the snap after the huddle is broken, are all great indicators of where the ball will end up.

<center><img src="https://raw.github.com/tysw01/NFL-Big-Data-Bowl-2025/main/IMAGES/pos_avg_predictions.png" width="800"/></center>
<center>Figure 7. Top 20 average player predictions by position (min. 3 red zone plays)</center>
<br>

The above chart shows offenses total predicted utilization of all players, filtered by positions. Using this along with the formation percentage charts, defenses can be able to:

1. See the offenses formation
   
2. Note which position is most likely to get the football depending on the offense

3. Use player predictions to decide which player to prioritize coverage on

## Conclusion and Applications

Attempting to predict who is going to get the ball on each play of the red zone is impossible to be perfect at. Things like plays getting blown up, players missing assignments after the snap, and receivers blowing by a defender in a one-on-one matchup can completely throw off the results of a play post-snap. However, being able to cover up the most likely option, can then allow teams to uncover what offenses will revert to as a second option. Defenses that can become consistent in showing the offense they know what's coming, can open up the opportunity to see their second choice tendencies. This model excels at using visualizations before the snap to predict which player is going to get the ball. The goal is to see if teams have certain patterns or tendencies in the red zone, and figuring those things out can allow defenses to get the upper hand.

## Further Developments and Limitations

**Further Developments**
In the future, there are many directions that an analyst could go in pertaining to this topic and findings. Some further developments that could be made are:

1. **Finding Team/Player Tells** - What "tells" do teams have as to where the football is going to end up? How can you determine their second or third options further? Do certain players line up or stand a certain way on each play?

2. **Offensive Changes in the Red Zone** - How can you predict changes in offensive play calling once teams enter the red zone?


## Appendix

**Limitations**

There are many uncertainties pertaining to this project. Teams constantly change coaching staff and rosters year-to-year. It is rare that rosters stay completely the same throughout multiple years. This results in consistent changes in play calling for each team. Teams adjust their offense depending on the team they are playing, so it is hard to say that teams offenses are run the same every single week. There are so many other things that play into how a game progresses each week. Bad weather can cause a team to run the ball more. Backup players and injuries can completely shift offensive schemes from week-to-week. During the last couple weeks of the season, teams change strategies based on draft order, playoff push or the week 18 benching of starters.

**Assumptions**
This project assumes that:

1. Offenses are aware of the defensive scheme (man or zone) prior to every play.
2. Schemes stay the same throughout the entirety of the game.
3. Defenses perform similarly in terms of output (This model doesn't take into account defensive statistics/rankings)

**Personal Disclosures**

I would like to disclose that I used artificial intelligence as a tool to assist in working out the "kinks" in my code. It was not used to alter results or create the project itself in any fashion. This is the largest project I've worked on, and I was simply unfamiliar with how to format some of the code when working with this much data.

Additionally, I'd like to thank the National Football League for the opportunity to create a project such as this. I had a blast competing against other people while doing it and learning from others who have worked in this competition in the past. I like to think of this analysis as a way of watching film and using player intuition, but with statistics instead. This is my first big data analytics project, and ultimately I'm very proud of myself and the product I've created. Feel free to reach out with any questions!

Code is available on GitHub [here](https://github.com/tysw01/NFL-Big-Data-Bowl-2025/tree/main).