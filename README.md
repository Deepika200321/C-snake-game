using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;

class Program
{
    static int width = 20; // Width of the game area
    static int height = 20; // Height of the game area
    static int score = 0; // Player's score
    static int snakeLength; // Length of the snake
    static List<int> snakeX = new List<int>(); // Snake's X coordinates
    static List<int> snakeY = new List<int>(); // Snake's Y coordinates
    static int foodX; // Food's X coordinate
    static int foodY; // Food's Y coordinate
    static int gameSpeed = 300; // Increased game speed (milliseconds)
    static Random rand = new Random(); // Random number generator
    static string direction = "RIGHT"; // Snake's initial direction

    static void Main()
    {
        Console.CursorVisible = false; // Hide the cursor
        InitializeGame();
        GameLoop();
    }

    static void InitializeGame()
    {
        snakeLength = 1; // Starting length of the snake
        snakeX.Add(width / 2); // Starting position
        snakeY.Add(height / 2);
        GenerateFood(); // Place food at a random location
    }

    static void GameLoop()
    {
        while (true)
        {
            if (Console.KeyAvailable) // Check for user input
            {
                ConsoleKeyInfo key = Console.ReadKey(true);
                ChangeDirection(key.Key); // Change direction based on input
            }

            UpdateGame(); // Update the game state
            if (CheckCollision()) // Check for collisions
            {
                GameOver(); // End the game if a collision occurs
                return;
            }

            Render(); // Render the game
            Thread.Sleep(gameSpeed); // Control game speed
        }
    }

    static void ChangeDirection(ConsoleKey key)
    {
        if (key == ConsoleKey.UpArrow && direction != "DOWN") direction = "UP";
        else if (key == ConsoleKey.DownArrow && direction != "UP") direction = "DOWN";
        else if (key == ConsoleKey.LeftArrow && direction != "RIGHT") direction = "LEFT";
        else if (key == ConsoleKey.RightArrow && direction != "LEFT") direction = "RIGHT";
    }

    static void UpdateGame()
    {
        int newHeadX = snakeX[0];
        int newHeadY = snakeY[0];

        // Update head position based on direction
        if (direction == "UP") newHeadY--;
        else if (direction == "DOWN") newHeadY++;
        else if (direction == "LEFT") newHeadX--;
        else if (direction == "RIGHT") newHeadX++;

        // Check for food consumption
        if (newHeadX == foodX && newHeadY == foodY)
        {
            snakeLength++;
            score++;
            GenerateFood(); // Generate new food
        }
        else
        {
            // Remove the tail if not eating
            snakeX.RemoveAt(snakeX.Count - 1);
            snakeY.RemoveAt(snakeY.Count - 1);
        }

        // Insert new head position
        snakeX.Insert(0, newHeadX);
        snakeY.Insert(0, newHeadY);
    }

    static bool CheckCollision()
    {
        // Check wall collisions
        if (snakeX[0] < 0 || snakeX[0] >= width || snakeY[0] < 0 || snakeY[0] >= height)
            return true;

        // Check self-collisions
        for (int i = 1; i < snakeLength; i++)
        {
            if (snakeX[0] == snakeX[i] && snakeY[0] == snakeY[i])
                return true;
        }

        return false; // No collision
    }

    static void GenerateFood()
    {
        foodX = rand.Next(0, width);
        foodY = rand.Next(0, height);
    }

    static void Render()
    {
        Console.Clear(); // Clear the console
        for (int i = 0; i < width + 2; i++) Console.Write("#"); // Draw top wall
        Console.WriteLine();

        for (int y = 0; y < height; y++)
        {
            Console.Write("#"); // Draw left wall
            for (int x = 0; x < width; x++)
            {
                if (snakeX[0] == x && snakeY[0] == y) Console.Write("O"); // Draw head
                else if (snakeX.Contains(x) && snakeY.Contains(y)) Console.Write("o"); // Draw body
                else if (x == foodX && y == foodY) Console.Write("F"); // Draw food
                else Console.Write(" "); // Empty space
            }
            Console.WriteLine("#"); // Draw right wall
        }

        for (int i = 0; i < width + 2; i++) Console.Write("#"); // Draw bottom wall
        Console.WriteLine($"\nScore: {score}"); // Display score
    }

    static void GameOver()
    {
        Console.Clear();
        Console.WriteLine($"Game Over! Your score is: {score}");
        Console.WriteLine("Press any key to exit...");
        Console.ReadKey();
    }
}
