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

![Tic Tac Toe Image](https://imgur.com/PduYtGR)

Here, it is O's turn, and, to a human, it is obvious which choice to make. The top left move will win the game, and the top middle will end in a draw. So in this case, the minimax algorithm would assign a positive score to the top left move, and it would give a score of zero to the top middle move. This is because that move will result in no winner. The size of the score doesn't matter. It could be +10 for a winning move, -10 for a losing move, and zero for a draw. For reasons I'll get to soon, you wouldn't want to pick +1, -1, and zero. There needs to be some room to push the scores up or a down a little.

But what does minimax do if this is not the final move in the game. It uses recursion. When it looks at the next move and sees it is not an immediate winner or loser, then the algorithm will "call itself" again for that next step, and it will keep generating new calls until it goes through enough moves to find a move that wins, loses, or draws the game. Once that move is found, then the score is sent back up the line of recursive calls until it gets back to the move the player is currently on. At that point, it can inform the player if the move is worth making. The algorithm assumes that the other player will be playing a perfect strategy as well.

I won't go into the exact details of my implementation (it did work!), because I'm sure they don't want a blog post that is basically a walkthrough of a challenging lab, but this really covers everything that is needed to build the algorithm. In the end, the `minimax` method is fairly simple. It's only about 12 lines of code, plus a small method that checks a turn to see if anyone has won and returns a score of +10, -10, or zero.

The last thing I will mention is an interesting wrinkle in this strategy that was discovered by the person who wrote one of the articles I read to learn about the algorithm. They described the algorithm so far as "perfect, but fatalistic". What they meant is that a computer player using these rules, when faced with a game they have no chance of winning, will immediately lose the game. The algorithm considered this the perfect strategy. In a strange way, it's probably correct. The algorithm is assuming the other player will also play perfectly, so if there is no chance of winning, then why would it waste its time prolonging the game. That would be...less than perfect.

But one thing we all know about human beings is that they will not *always* perform perfectly, so if the algorithm is expected to also play against humans, then it should continue playing in case the human makes an error and turns a sure thing into a potential loss.

The article had a very simple solution to this problem. When calculating the scores, simply subtract the number of turns (or what they called the depth) from each score. This will make the computer considered losing moves that are many turns away less important than moves that might win in a few turns. This way the computer will ignore an "inevitable" loss that requires the opposing player to continue playing perfectly. This is why you wouldn't want to use +1, -1, and zero. You would have to subtract something like 0.1 from each score for each layer of depth. it makes more sense to simply subtrat the actual number of turns, which is a clean whole number. Finally, make sure to subtract the number of turns from the computer, but *add* the number of turns to the opposing player. Their scores are negative, so to make those scores less important to the algorithm, they need to be moved closer to zero, which means adding. 


