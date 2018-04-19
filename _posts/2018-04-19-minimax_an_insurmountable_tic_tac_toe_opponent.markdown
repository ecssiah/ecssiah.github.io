---
layout: post
title:      "Minimax: An Insurmountable Tic Tac Toe Opponent"
date:       2018-04-19 01:55:34 -0400
permalink:  minimax_an_insurmountable_tic_tac_toe_opponent
---


When the introduction to the lab hinted there was an unbeatable AI algorithm for Tic Tac Toe, but it would be awkward and difficult to implement, I knew I had to do it. It didn't take long to find a few excellent articles that explained clearly how the minimax algorithm chooses the perfect strategy. Now I just had to fit it into the established framework of the lab.

It's not that hard to understand *what* minimax is doing, but it may be a little harder to understand *how* it is doing it.

The basic premise is that the algorithm plays out every move available at each step for both players, and it comes up with a score for each move the player could make. It arrives at this score by giving good moves for the opponent a negative score and good moves for the player a positive score. Whichever move comes out positive for the player is the one it picks.

The easiest first step to understanding how this works is to look at a game that is almost over, because there is only a few decisions left to make.

![Tic Tac Toe Decision](https://imgur.com/PduYtGR)


