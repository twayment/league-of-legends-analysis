# League of Legends Role Analysis

by Trey Wayment (twayment@umich.edu)

This is the final project for EECS 398 at the University of Michigan

## Introduction

This project uses post-game data from professional League of Legends (LoL) matches played in 2022 to explore how player roles affect performance. The dataset comes from [Oracle’s Elixir](https://oracleselixir.com). Each match has 12 relevant rows: one for each of the 5 players on both teams and 2 containing summary data for the two teams. The dataset contains 150,588 rows and 161 columns.

My project is centered around the question:
**Which role “carries” (does the best) in their team more often: ADCs (Bot lanes) or Mid laners?**

This question is interesting because it's something that League players constantly debate — do ADCs or Mid laners carry more often? By digging into real pro-level data, we can actually get some objective insight into that debate.

This dataset is valuable because it captures how different roles behave statistically in high-level play. That kind of information is helpful for coaches, analysts, and players trying to understand what performance looks like in each position.

The following columns from the dataset are most relevant to my analysis:
- **assists**: Number of kills the player assisted.
- **champion**: The champion played by the player.
- **cspm**: Creep Score per Minute — minions killed per minute, indicating farm efficiency.
- **damageshare**: Percentage of team damage dealt by the player — a measure of impact.
- **deaths**: Number of times the player died.
- **kills**: Number of kills the player secured.
- **position**: The player's in-game role.
- **totalgold**: Total gold earned in the game — reflects resource efficiency.
- **visionscore**: A metric measuring vision control (e.g., wards placed/cleared).

## Data Cleaning and Exploratory Data Analysis

todo