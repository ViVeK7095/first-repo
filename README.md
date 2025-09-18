# first-repo
this is my first repository

import math

class TicTacToe:
    def _init_(self):
        self.board = [' ' for _ in range(9)]  # 3x3 board as a list (0-8)
        self.current_winner = None

    def print_board(self):
        for row in [self.board[i*3:(i+1)*3] for i in range(3)]:
            print('| ' + ' | '.join(row) + ' |')

    @staticmethod
    def print_board_nums():
        number_board = [[str(i) for i in range(j*3, (j+1)*3)] for j in range(3)]
        for row in number_board:
            print('| ' + ' | '.join(row) + ' |')

    def available_moves(self):
        return [i for i, spot in enumerate(self.board) if spot == ' ']

    def empty_squares(self):
        return ' ' in self.board

    def num_empty_squares(self):
        return self.board.count(' ')

    def make_move(self, square, letter):
        if self.board[square] == ' ':
            self.board[square] = letter
            if self.winner(square, letter):
                self.current_winner = letter
            return True
        return False

    def winner(self, square, letter):
        # Check row
        row_ind = square // 3
        row = self.board[row_ind*3:(row_ind+1)*3]
        if all([spot == letter for spot in row]):
            return True

        # Check column
        col_ind = square % 3
        column = [self.board[col_ind+i*3] for i in range(3)]
        if all([spot == letter for spot in column]):
            return True

        # Check diagonal
        if square % 2 == 0:
            diagonal1 = [self.board[i] for i in [0, 4, 8]]
            if all([spot == letter for spot in diagonal1]):
                return True
            diagonal2 = [self.board[i] for i in [2, 4, 6]]
            if all([spot == letter for spot in diagonal2]):
                return True
        return False

def minimax(game, maximizing, alpha=float('-inf'), beta=float('inf')):
    # Base cases
    if game.current_winner == 'O':
        return {'position': None, 'score': 1 * (game.num_empty_squares() + 1)}
    elif game.current_winner == 'X':
        return {'position': None, 'score': -1 * (game.num_empty_squares() + 1)}
    elif not game.empty_squares():
        return {'position': None, 'score': 0}

    if maximizing:
        best = {'position': None, 'score': -math.inf}
        for possible_move in game.available_moves():
            game.make_move(possible_move, 'O')
            sim_score = minimax(game, False, alpha, beta)
            game.board[possible_move] = ' '
            game.current_winner = None
            sim_score['position'] = possible_move
            if sim_score['score'] > best['score']:
                best = sim_score
            alpha = max(alpha, best['score'])
            if beta <= alpha:
                break
        return best
    else:
        best = {'position': None, 'score': math.inf}
        for possible_move in game.available_moves():
            game.make_move(possible_move, 'X')
            sim_score = minimax(game, True, alpha, beta)
            game.board[possible_move] = ' '
            game.current_winner = None
            sim_score['position'] = possible_move
            if sim_score['score'] < best['score']:
                best = sim_score
            beta = min(beta, sim_score['score'])
            if beta <= alpha:
                break
        return best

def play_game():
    game = TicTacToe()
    print("Welcome to Tic-Tac-Toe! You are X, AI is O.")
    print("Enter a number (0-8) to make a move:")
    game.print_board_nums()

    while game.empty_squares():
        # Human move
        valid_square = False
        while not valid_square:
            try:
                square = int(input('Your move (0-8): '))
                if square not in game.available_moves():
                    print("Invalid move. Try again.")
                else:
                    valid_square = True
            except ValueError:
                print("Please enter a number between 0 and 8.")

        game.make_move(square, 'X')
        game.print_board()
        if game.current_winner == 'X':
            print("You win!")
            return

        if not game.empty_squares():
            print("It's a tie!")
            return

        # AI move
        print("AI's move...")
        move = minimax(game, True)['position']
        game.make_move(move, 'O')
        game.print_board()
        if game.current_winner == 'O':
            print("AI wins!")
            return

if _name_ == "_main_":
    play_game()
