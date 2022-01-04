---
title: 'Competitive fighting game with heuristic-based AI'
date: 2022-01-04
permalink: /posts/2022/1/heuristic_ai/
tags:
  - python
  - pygame
  - AI
---

I created a 1v1 fighting game in Python using [PyGame](https://www.pygame.org/wiki/about). Included is a 1-player option that allows you to play against a challenging AI opponent. It can make fast decisions based on many different factors of the current game state. I personally lose to it almost half of the time.  

How do you program a character in a video game to make reasonable decisions automatically? A common method is to use [heuristics](https://optimization.mccormick.northwestern.edu/index.php/Heuristic_algorithms). This allowa you to break down complex problems into simple decisions. They may not provide optimal solutions, but they are often fast and easy to understand. Both of these features are useful for developing a competitive enemy in a fighting game.

## Developing the game

Before creating the AI, I had to develop the game itself. I wanted to make a fighting game because it seemed like an interesting environment for an AI to operate in.  

#### Game code

Programming a game actually has a lot of similarities to another type of programming I've done a lot of: programming cognitive psychology experiments. There are many commonalities including handling 'player' input, 'game' states, blitting (or drawing) objects on the screen, and providing intuitive and informative feedback. 

I choose [PyGame](https://www.pygame.org/wiki/about) to develop the game because I love Python and PyGame is far and away the most common game development framework in Python. 

The main game loop basically consists of getting player input, updating the state of the game, and then drawing everything onscreen. If your game runs at 60 frames per second, that loop happens every 1.67 milliseconds. So for my game, each loop must get the input from the players (human or AI), update the game state (collisions, damage, etc.), and then draw everything on the screen (the background, characters, weapons, health, and stamina).

![](https://williamthyer.github.io/images/heuristics_ai/game_loop.png)

I won't go into too much detail about the code itself, but you can check out the full [github repo here](https://github.com/WilliamThyer/Vorpal). There were a lot of interesting challenges I faced during development. Player collisions, scaling the game to different screen sizes, and dashing and jumping physics all required a lot of planning and trial and error.

#### The game itself

The game is a basic fighting game. Two characters with 5 health face off against each other. The last one standing wins.

Each character can strike with a sword, block with a shield, jump, and dash. They have 5 health, and 5 stamina. Each action costs a stamina and stamina slowly regenerates. Successfully blocking an attack causes the attacker to lose all of their stamina. This system provides more than enough for a complex game that's still tractable for me to develop myself.

## Developing the AI

Now that I had the game developed, I could work on the AI. I modified the code with an `AIEnemy` class. In 2-player, two humans play against each other. In 1-player, a human plays against this `AIEnemy`. During the player input portion of the game loop, the `AIEnemy` provides its own input.

#### Random input 
I wanted to create a baseline system to compare any progress with. My first iteration was as simple as it gets. Every frame, the `AIEnemy` class returns one random input out of the possible inputs (move left, move right, jump, sword strike, sword strike downwards, and shield). This results in a gittering mess. Because left and right are equally likely, it tends to just move back and forth while occassionaly striking, shielding, and jumping.

[insert random input gif]

#### Random sequence

A slightly more sophisticated approach would be to create sequences of movements. During the `AIEnemy` initialization, a series of sequences are created: `walk right`, `walk left`, `jump right`, `jump left`, `sword`, `shield`, `jump left downwards strike`, and `jump right downwards strike`. Each of these sequences provides somewhere from 1 to 10 frames of input. Even though the sequences are still randomly chosen, it is somewhat more coherent. 

[insert random sequence gif]

#### Heuristics

Finally, I started working on the heuristic approach. This system could still use the sequences, but they wouldn't be chosen randomly. Instead, they would be chosen based on a variety of factors of the current game state. The most important factors are the positions of the players, the amount of stamina, and the current states of the players (sword striking, shielding, stunned, etc.). 

Each frame, the `AIEnemy` makes a decision based on these factors. For example, if the player is far away the AI walks towards them. If they are "medium distance" the AI either walks towards them, dashes towards them, or jumps and strikes downwards. Once close, the AI will walk towards them or strike.

Notice that I say "or" for some of these circumstances. I chose to implement some randomness in the decision-making process. That keeps things harder to predict and also avoids annoying loops where the AI does the same thing over and over.

One major problem with this set of rules is that it creates an overly aggressive fighter. It only approaches and attacks, even if it has no stamina to do so. So I implemented an `avoid` function. Basically, if the AI character has no stamina, it runs away from the player.

#### Adding sequence breaking
Sometimes, a decision has to be made immediately. That includes blocking a strike or attacking a stunned player. So I implemented a functionality I called a `sequence break`. If a particular condition is reached, whatever sequence is currently being executed is broken immediately and a new sequence is begun.

This drastically increased the reactivity of the AI. Between the randomness and the sequence breaking, the `EnemyAI` had become a seriously tough opponent. By this point, I was losing a significant portion of the fights. And not only that, but it was making reasonable decisions that seemed almost human. That seemed like the gold standard of a good AI opponent.

## Future directions

I had a blast working on this project. Making the game was frustrating at times because it required so much attention to detail. Nothing happens in a game unless you explicitly make it happen. But once that was working, developing the AI was really interesting. It took a lot of trial and error, but iterating on the decision making process of the AI was fun. 

The most obvious next step would be to take the AI to the next level by using reinforcement learning. There are a few really nice tutorials on using reinforcement learning with PyGame and [OpenAI Gym](https://gym.openai.com/) ([here](https://towardsdatascience.com/ultimate-guide-for-reinforced-learning-part-1-creating-a-game-956f1f2b0a91) and [here](https://www.learndatasci.com/tutorials/reinforcement-q-learning-scratch-python-openai-gym/)) or PyTorch ([here](https://www.python-engineer.com/posts/teach-ai-snake-reinforcement-learning/)).
