## TicTacToe

```python
import random

def check_winner(player, board):
    """
    Check if the player has won the game.

    Args:
        player (str): The player ('x' or 'o') to check for a win.
        board (list): The current state of the game board.

    Returns:
        bool: True if the player has won, False otherwise.
    """
    # Check rows and columns
    for i in range(3):
        if all(board[i][j] == player for j in range(3)) or all(board[j][i] == player for j in range(3)):
            return True
    # Check diagonals
    return (all(board[i][i] == player for i in range(3)) or all(board[i][2 - i] == player for i in range(3)))

def next_move(board):
    """
    Determine the next move for the computer player.

    Args:
        board (list): The current state of the game board.

    Returns:
        None
    """
    for i in range(3):
        for j in range(3):
            if board[i][j] == ' ':
                board[i][j] = 'o'
                if check_winner('o', board):
                    return
                board[i][j] = 'x'
                if check_winner('x', board):
                    board[i][j] = 'o'
                    return
                board[i][j] = ' '
    # If no winning move, make a random move
    while True:
        row = random.randint(0, 2)
        column = random.randint(0, 2)
        if board[row][column] == ' ':
            board[row][column] = 'o'
            break

def is_board_full(board):
    """
    Check if the game board is full.

    Args:
        board (list): The current state of the game board.

    Returns:
        bool: True if the board is full, False otherwise.
    """
    return all(cell != ' ' for row in board for cell in row)

def display_board(board):
    """
    Display the current state of the game board.

    Args:
        board (list): The current state of the game board.

    Returns:
        None
    """
    for row in board:
        print(row)

def main():
    """
    Main function to run the tic-tac-toe game.
    """
    board = [[' ' for _ in range(3)] for _ in range(3)]
    print("You are playing as 'x'")
    while True:
        display_board(board)
        print("Player's Move")
        row = int(input("Enter the row: "))
        column = int(input("Enter the column: "))
        if board[row][column] == ' ':
            board[row][column] = 'x'
            if check_winner('x', board):
                display_board(board)
                print("You Have Won!!")
                break
            elif is_board_full(board):
                display_board(board)
                print("It's a Draw!")
                break
            print("Computer's Move")
            next_move(board)
            if check_winner('o', board):
                display_board(board)
                print("Computer has Won!!")
                break
            elif is_board_full(board):
                display_board(board)
                print("It's a Draw!")
                break
        else:
            print("Enter correct choice")

if __name__ == '__main__':
    main()
```

---

## Nim Game

```python
import random

def main():
    rockList = []
    randPile = random.randint(2, 5)
    name1, name2 = input("Enter player 1 name: "), input("Enter player 2 name: ")
    player = name1
    
    print_board(randPile, rockList)
    get_valid_input(rockList, player)

def print_board(randPile, rockList):
    print("Let's look at the board now.")
    print("-" * 25)
    for i in range(randPile):
        randRock = random.randint(1, 8)
        print(f"Pile {i+1}: {'O' * randRock}")
        rockList.append(randRock)
    print("-" * 25)
    nim_sum(rockList)

def get_valid_input(rockList, player):
    while True:
        stones = input(f"{player}, how many stones to remove? ")
        piles =  input('Pick a pile to remove from: ')
        if stones.isdigit() and piles.isdigit():
            stones, piles = int(stones), int(piles)
            if 0 < stones <= rockList[piles - 1]:
                rockList[piles - 1] -= stones
                continue_game(rockList, player)
                break
        print(f"Invalid input, try again, {player}.")

def continue_game(rockList, player): 
    print("Let's look at the board now.")
    print("-" * 25)
    for i, stones in enumerate(rockList):
        print(f"Pile {i + 1}: {'O' * stones}")
    print("-" * 25)

    if rockList != [0] * len(rockList):
        nim_sum(rockList)
        get_valid_input(rockList, player)
    else:
        print(f"{player} is the winner of this round!")
        play_again()

def play_again():
    user = input("Do you want to play again? Enter y for yes, anything for no: ")
    if user.lower() == 'y':
        main()
    else:
        print("Game over!")

def nim_sum(rockList):
    nim = 0
    for stones in rockList:
        nim ^= stones
    
    print(f"Hint: nim sum is {nim}.")

    if nim == 0:
        print(f"Pick all stones from pile {rockList.index(max(rockList)) + 1}.")
    else:
        max_pile = rockList.index(max(rockList)) + 1
        stones_to_remove = max(rockList) - nim
        stones_to_remove = max(1, stones_to_remove)
        print(f"Pick {stones_to_remove} stones from pile {max_pile}.")

main()
```

---

 ## A* Algorithm 

```python
import heapq

def astar(grid, start, goal):
    """
    Find the shortest path from the start to the goal using A* algorithm.

    Args:
        grid (list): The grid representing the map or maze.
        start (tuple): The coordinates of the starting node.
        goal (tuple): The coordinates of the goal node.

    Returns:
        list or None: A list of coordinates representing the shortest path
                      from start to goal, or None if no path is found.
    """
    open_list = [(0, start)]
    parent = {}
    cost_from_start = {start: 0}

    while open_list:
        current_cost, current_node = heapq.heappop(open_list)

        if current_node == goal:
            return reconstruct_path(parent, current_node)

        for neighbor in get_neighbors(grid, current_node):
            new_cost = cost_from_start[current_node] + 1
            if neighbor not in cost_from_start or new_cost < cost_from_start[neighbor]:
                cost_from_start[neighbor] = new_cost
                priority = new_cost + heuristic(neighbor, goal)
                heapq.heappush(open_list, (priority, neighbor))
                parent[neighbor] = current_node

    return None

def get_neighbors(grid, node):
    """
    Get valid neighbors of a given node in the grid.

    Args:
        grid (list): The grid representing the map or maze.
        node (tuple): The coordinates of the node.

    Returns:
        list: A list of valid neighboring nodes.
    """
    neighbors = []
    for d_row, d_col in [(0, 1), (0, -1), (1, 0), (-1, 0),(1,1),(1,-1),(-1,1),(-1,-1)]:
        new_row, new_col = node[0] + d_row, node[1] + d_col
        if 0 <= new_row < len(grid) and 0 <= new_col < len(grid[0]) and grid[new_row][new_col] == 1:
            neighbors.append((new_row, new_col))
    return neighbors

def reconstruct_path(parent, goal):
    """
    Reconstruct the path from start to goal using the parent dictionary.

    Args:
        parent (dict): A dictionary mapping nodes to their parent nodes.
        goal (tuple): The coordinates of the goal node.

    Returns:
        list: A list of coordinates representing the path from start to goal.
    """
    path = []
    current_node = goal
    while current_node in parent:
        path.append(current_node)
        current_node = parent[current_node]
    return path[::-1]

def heuristic(a, b):
    """
    Calculate the heuristic (Manhattan distance) between two points.

    Args:
        a (tuple): Coordinates of the first point.
        b (tuple): Coordinates of the second point.

    Returns:
        int: The Manhattan distance between the two points.
    """
    return abs(a[0] - b[0]) + abs(a[1] - b[1])

if __name__ == '__main__':
    grid = [
        [1, 0, 1, 1, 1, 1, 0, 1, 1, 1],
        [1, 1, 1, 0, 1, 1, 1, 0, 1, 1],
        [1, 1, 1, 0, 1, 1, 0, 1, 0, 1],
        [0, 0, 1, 0, 1, 0, 0, 0, 0, 1],
        [1, 1, 1, 0, 1, 1, 1, 0, 1, 0],
        [1, 0, 1, 1, 1, 1, 0, 1, 0, 0],
        [1, 0, 0, 0, 0, 1, 0, 0, 0, 1],
        [1, 0, 1, 1, 1, 1, 0, 1, 1, 1],
        [1, 1, 1, 0, 0, 0, 1, 0, 0, 1]
    ]
    start = (8, 0)
    goal = (1, 4)
    path = astar(grid, start, goal)
    if path:
        print("Path found:", path)
    else:
        print("No path found")

```

---

 ## Alpha-Beta pruning

---

```python
MAX, MIN = 1000, -1000

def minimax(depth, nodeIndex, maximizingPlayer, values, alpha, beta):
    if depth == 3:
        return values[nodeIndex]

    if maximizingPlayer:
        best = MIN
        for i in range(2):
            val = minimax(depth + 1, nodeIndex * 2 + i, False, values, alpha, beta)
            best = max(best, val)
            alpha = max(alpha, best)
            if beta <= alpha:
                break
        return best
    
    else:
        best = MAX
        for i in range(2):
            val = minimax(depth + 1, nodeIndex * 2 + i, True, values, alpha, beta)
            best = min(best, val)
            beta = min(beta, best)
            if beta <= alpha:
                break
        return best

if __name__ == "__main__":
    values = [3, 5, 6, 9, 1, 2, 0, -1]
    print("The optimal value is :", minimax(0, 0, True, values, MIN, MAX))

```

---
