---
layout: post
title:      "Minimax: An Insurmountable Tic Tac Toe Opponent"
date:       2018-04-19 01:55:34 -0400
permalink:  minimax_an_insurmountable_tic_tac_toe_opponent
---


When the introduction to the lab hinted there was an unbeatable AI algorithm for Tic Tac Toe, but it would be challenging to implement, I knew I had to do it. It didn't take long to find a few excellent articles that explained how the minimax algorithm chooses the perfect strategy. Now I just had to fit this into the framework of the lab.

It's not that hard to understand *what* minimax is doing, but it may be a little harder to understand *how* it is doing it.

**What it's doing...**

The basic premise is that the algorithm assumes each player will play perfectly, and it plays out every move that will be available for both players. It assigns each move a score, based on whether it eventually leads to a win. It gives winning moves a positive score, losing moves a negative score, and counts draws as zero. The first move that comes out positive for the player is the one it picks each turn.

**How it's doing it...**

The easiest first step to understanding how this works is to look at a game that is almost over, because there is only a few decisions left to make.

![Tic Tac Toe Image](https://imgur.com/PduYtGR)

Here, it is O's turn, and, to a human, it is obvious which choice to make. The top left move will win the game, and the top middle will end in a draw. So in this case, the minimax algorithm would assign a positive score to the top left move, and it would give a score of zero to the top middle move. The size of the score doesn't matter. It could be +10 for a winning move, -10 for a losing move, and zero for a draw. For reasons I'll get to, you wouldn't want to pick +1, -1, and zero. There needs to be some room to push the scores up or a down a little.

But what does minimax do if this is not the final move in the game. It uses recursion. When it looks at the next move and sees it is not an immediate winner or loser, then the algorithm will "call itself" again using the next move as a new starting point, and it will keep generating new calls until it goes through enough moves to find one that wins, loses, or draws the game. Once that move is found, then the score is sent back up the line of recursive calls until it gets back to the move the player is currently on. At that point, it can inform the player if the move is worth making. The algorithm assumes that the other player will also be playing perfectly.

I won't go into the exact details of my implementation (it did work!), because I'm sure they don't want a blog post that is basically a walkthrough of a challenging lab, but this really covers everything that is needed to build the algorithm. In the end, the `minimax` method is fairly simple. It's only about 12 lines of code, plus a small method that checks the board to see if anyone has won and returns a score of +10, -10, or zero.

The last thing I will mention is an interesting wrinkle in this strategy that was discovered by the person who wrote one of the articles I read to learn about the algorithm. They described the algorithm so far as "perfect, but fatalistic". What they meant is that a computer player using these rules, when faced with a game they have no chance of winning, will immediately lose the game. The algorithm considered this the perfect strategy. In a strange way, it's probably correct. The algorithm is assuming the other player will also play perfectly, so if there is no chance of winning, then why would it waste its time prolonging the game. That would be...less than perfect.

But one thing we all know about human beings is that they will not *always* perform perfectly, so if the algorithm is expected to also play against humans, then it should continue playing in case the human makes an error and turns a sure thing into a potential loss.

The article had a very simple solution to this problem. When calculating the scores, simply subtract the number of turns the algorithm is looking ahead (or what they called the depth) from each score. So if a move will win 4 turns from now, it is actually given a score of 6, but a move that will win this turn is given a score of 10. This will make the computer consider losing moves that are many turns away less important than moves that might win in a few turns. This way the computer will ignore an "inevitable" loss that requires the opposing player to continue playing perfectly. This is why you wouldn't want to use +1, -1, and zero. You would have to subtract something like 0.1 from each score for each layer of depth. it makes more sense to simply subtrat the actual number of turns, which is a clean whole number. Finally, make sure to subtract the number of turns from the AI's score but *add* the number of turns to the opposing player's scores. Their scores are negative, so to make those scores less important to the algorithm, they need to be moved closer to zero, which means adding.

If you implement this algorithm correctly, then two computer players facing each other will **always** draw. It's impressive to look at the number of moves the algorithm has to search through when looking at a board. There are thousands of possible games it needs to look through.


