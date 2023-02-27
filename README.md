# Hamster Holes

Welcome to Hamster Holes, an exciting grid-based game where your goal is to hit as many hamsters as you can within sixty seconds.

## Getting Started

To run this project locally, follow these steps:

``` git clone https://github.com/your-username/hamster-holes.git
cd hamster-holes
npm install
npm run dev
 ```

## How to Play

The game consists of 36 holes, each of which can randomly spawn a hamster, a snake, or a power-up. To score points, use your cursor to hit the hamsters that appear. But be careful! If you hit a snake, you will lose one of your three lives. As you score more points, the number of snakes that appear will increase, making the game more challenging.

To help you along the way, there are power-ups that can give you an extra life or ten extra seconds of gameplay time. You have sixty seconds to score as many points as possible, collect power-ups, and avoid the snakes.

Good luck and have fun playing Hamster Holes!

## Check out the gameplay

![App Screenshot](https://i.ibb.co/SBctThR/Screenshot-from-2023-02-26-14-18-36.png)

## How the Game Works

The game uses web technolgies  html,css,js and svelte for the development of the game. First starting with grid of holes it just a array of 36 elements and every element is just a object containing the sprite image and info about Sprites (Hamster,Snake,Bonus) presence using booling values. In the update function we generate a random index for hamster after that we generate random numbers for snakes and if the snakes random number is not equal to hamster's random number we push it in the snake_rand_stack after that update function first reset the grid then after that update the the grid using the unique indexes of hamster and snakes to display the animation in grid. The update function is always running even game is not started or gameover because of setInterval function why it's keep running because the game uses the same mechanism as the lua + Love2D games and other code-based game engine uses function updates with delta time and keeps on running as per frame rate of the game.And about the bonus part they uses same mechanism as Hamster and the Snake but they also have probility stack to make them appear them randomly and avoid often apperance probility of getting bonus increases as the number of snakes increase in the starting of game probablity is about 3/100.

Every cell in the grid is just a standard HTML button when we click on it the handle_click function checks the presence of sprite and than it updates score and life counts of the player. 
