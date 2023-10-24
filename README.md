# CODSOFTT

#TASK 1- CHATBOT WITH RULE-BASED RESPONSES

rules_responses = {
    "hello": "Hi there! How can I help you?",
    "how are you": "I'm just a chatbot, but thanks for asking!",
    "bye": "Goodbye! Feel free to come back if you have more questions.",
    "what is your name": "I'm just a simple rule-based chatbot.",
    "help": "I'm here to provide information and answer your questions. Just ask!"
}

# Function to generate responses
def get_response(user_input):
    user_input = user_input.lower()  # Convert input to lowercase for case-insensitivity
    response = rules_responses.get(user_input, "I'm sorry, I don't understand that. Please ask a different question.")
    return response

# Main chat loop
print("Chatbot: Hello! I'm a simple rule-based chatbot. You can ask me questions or say hi.")
while True:
    user_input = input("You: ")
    if user_input.lower() == "exit":
        print("Chatbot: Goodbye!")
        break
    response = get_response(user_input)
    print("Chatbot:", response)





    #TASK 2- TIC TAC TOE AI


    # Create the Tic-Tac-Toe board
board = [" " for _ in range(9)]

# Function to print the board
def print_board(board):
    for i in range(0, 9, 3):
        print(" | ".join(board[i:i+3]))
        if i < 6:
            print("-" * 9)

# Function to check if a player has won
def check_win(board, player):
    # Check rows, columns, and diagonals for a win
    win_patterns = [[0, 1, 2], [3, 4, 5], [6, 7, 8], [0, 3, 6], [1, 4, 7], [2, 5, 8], [0, 4, 8], [2, 4, 6]]
    for pattern in win_patterns:
        if all(board[i] == player for i in pattern):
            return True
    return False

# Function to check if the board is full
def check_draw(board):
    return " " not in board

# Function for the AI's move using Minimax
def ai_move(board, ai_player, human_player):
    best_score = -float("inf")
    best_move = None

    for i in range(9):
        if board[i] == " ":
            board[i] = ai_player
            score = minimax(board, 0, False, ai_player, human_player)
            board[i] = " "
            if score > best_score:
                best_score = score
                best_move = i

    return best_move

# Minimax algorithm
def minimax(board, depth, is_maximizing, ai_player, human_player):
    if check_win(board, ai_player):
        return 1
    if check_win(board, human_player):
        return -1
    if check_draw(board):
        return 0

    if is_maximizing:
        best_score = -float("inf")
        for i in range(9):
            if board[i] == " ":
                board[i] = ai_player
                score = minimax(board, depth + 1, False, ai_player, human_player)
                board[i] = " "
                best_score = max(score, best_score)
        return best_score
    else:
        best_score = float("inf")
        for i in range(9):
            if board[i] == " ":
                board[i] = human_player
                score = minimax(board, depth + 1, True, ai_player, human_player)
                board[i] = " "
                best_score = min(score, best_score)
        return best_score

# Main game loop
ai_player = "X"
human_player = "O"

while True:
    print_board(board)

    if check_draw(board):
        print("It's a draw!")
        break

    human_move = int(input("Enter your move (0-8): "))
    if board[human_move] != " ":
        print("Invalid move. Try again.")
        continue

    board[human_move] = human_player

    if check_win(board, human_player):
        print_board(board)
        print("Congratulations! You win!")
        break

    ai_move = ai_move(board, ai_player, human_player)
    board[ai_move] = ai_player

    if check_win(board, ai_player):
        print_board(board)
        print("AI wins! Try again.")
        break



#TASK 3- Recomendation system


import pandas as pd
from surprise import Dataset, Reader
from surprise.model_selection import train_test_split
from surprise import SVD
from surprise import accuracy

# Load the MovieLens dataset
# You can download the dataset from: https://grouplens.org/datasets/movielens/
ratings = pd.read_csv('ratings.csv')  # Replace with your dataset location
# If you don't have the dataset, you can use the built-in MovieLens dataset provided by Surprise.

# Define a custom dataset
reader = Reader(rating_scale=(1, 5))
data = Dataset.load_from_df(ratings[['userId', 'movieId', 'rating']], reader=reader)

# Split the dataset into a training set and a testing set
trainset, testset = train_test_split(data, test_size=0.2)

# Create a recommendation model (SVD, a matrix factorization method)
model = SVD(n_factors=100, n_epochs=20, verbose=True)

# Train the model on the training set
model.fit(trainset)

# Make recommendations for a specific user
user_id = 1  # Replace with the user ID for whom you want to make recommendations
user_movies = ratings[ratings['userId'] == user_id]['movieId']
unseen_movies = list(set(ratings['movieId']) - set(user_movies))

predictions = [model.predict(user_id, movie_id) for movie_id in unseen_movies]

# Sort the predictions by estimated ratings in descending order
predictions.sort(key=lambda x: x.est, reverse=True)

# Get the top N movie recommendations
top_n = 10  # Number of recommendations to make
top_predictions = predictions[:top_n]

# Display the top N movie recommendations
for prediction in top_predictions:
    print(f'Movie ID: {prediction.iid}, Estimated Rating: {prediction.est}')

# Evaluate the model's performance
test_predictions = model.test(testset)
rmse = accuracy.rmse(test_predictions)
mae = accuracy.mae(test_predictions)
print(f'RMSE: {rmse}')
print(f'MAE: {mae}')

