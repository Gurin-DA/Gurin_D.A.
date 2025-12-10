"""
Игра "Крестики-нолики" против компьютера
"""

import random

def print_board(board):
    print("\n   1   2   3")
    for i, row in enumerate(board, 1):
        # Заменяем 'O' на '0' при выводе
        display_row = ['0' if cell == 'O' else cell for cell in row]
        print(f"{i}  {' | '.join(display_row)}")
        if i < 3:
            print("  ---|---|---")

def check_winner(board):
    for i in range(3):
        if board[i][0] == board[i][1] == board[i][2] != ' ':
            return board[i][0]
    
    for i in range(3):
        if board[0][i] == board[1][i] == board[2][i] != ' ':
            return board[0][i]
    
    if board[0][0] == board[1][1] == board[2][2] != ' ':
        return board[0][0]
    if board[0][2] == board[1][1] == board[2][0] != ' ':
        return board[0][2]
    
    if all(cell != ' ' for row in board for cell in row):
        return 'draw'
    
    return None

def get_player_move(board, symbol):
    display_symbol = '0' if symbol == 'O' else symbol
    
    while True:
        try:
            row = int(input(f"Введите номер строки (1-3) для {display_symbol}: "))
            col = int(input(f"Введите номер столбца (1-3) для {display_symbol}: "))
            
            if 1 <= row <= 3 and 1 <= col <= 3:
                if board[row-1][col-1] == ' ':
                    return row-1, col-1
                else:
                    print("Эта клетка уже занята. Попробуйте снова.")
            else:
                print("Ошибка: числа должны быть от 1 до 3.")
        except ValueError:
            print("Ошибка: введите целые числа.")

def computer_move(board, computer_symbol):
    player_symbol = 'X' if computer_symbol == 'O' else 'O'
    
    # Может ли компьютер выиграть
    for i in range(3):
        for j in range(3):
            if board[i][j] == ' ':
                board[i][j] = computer_symbol
                if check_winner(board) == computer_symbol:
                    board[i][j] = ' '
                    return i, j
                board[i][j] = ' '
    
    # Проверим, не выиграет ли игрок следующим ходом
    for i in range(3):
        for j in range(3):
            if board[i][j] == ' ':
                board[i][j] = player_symbol
                if check_winner(board) == player_symbol:
                    board[i][j] = ' '
                    return i, j
                board[i][j] = ' '
    
    # Если центр свободен, занять его
    if board[1][1] == ' ':
        return 1, 1
    
    # Если углы свободны, занять случайный угол
    corners = [(0,0), (0,2), (2,0), (2,2)]
    empty_corners = [c for c in corners if board[c[0]][c[1]] == ' ']
    if empty_corners:
        return random.choice(empty_corners)
    
    # Иначе занять любую свободную клетку
    empty_cells = []
    for i in range(3):
        for j in range(3):
            if board[i][j] == ' ':
                empty_cells.append((i, j))
    
    if empty_cells:
        return random.choice(empty_cells)
    
    return None

def choose_symbol():
    print("\n" + "=" * 50)
    print("КРЕСТИКИ - X, ходят первыми")
    print("НОЛИКИ - обозначаются цифрой 0")
    print("=" * 50)
    
    while True:
        choice = input("Вы хотите играть крестиками (X) или ноликами (0)? Введите X или 0: ").upper()
        
        if choice == 'X':
            print("\nВы играете крестиками (X) и ходите ПЕРВЫМ")
            print("Компьютер играет ноликами (0)")
            return 'X', 'O'
        elif choice == '0':
            print("\nВы играете ноликами (0) и ходите ВТОРЫМ!")
            print("Компьютер играет крестиками (X) и ходит ПЕРВЫМ")
            return 'O', 'X'
        else:
            print("Пожалуйста, введите X или 0")

def play_game():
    board = [[' ' for _ in range(3)] for _ in range(3)]
    
    print("=" * 40)
    print("ИГРА 'КРЕСТИКИ-НОЛИКИ' ПРОТИВ КОМПЬЮТЕРА")
    print("=" * 40)
    
    # Выбор символов
    player_symbol, computer_symbol = choose_symbol()
    
    # Чей первый ход (всегда X ходит первым)
    if player_symbol == 'X':
        player_turn = True
        print("\nВаш ход первый!")
    else:
        player_turn = False
        print("\nКомпьютер ходит первым!")
    
    print("-" * 40)
    
    while True:
        print_board(board)
        
        if player_turn:
            # Ход игрока
            display_symbol = '0' if player_symbol == 'O' else player_symbol
            print(f"\n--- Ход игрока ({display_symbol}) ---")
            row, col = get_player_move(board, player_symbol)
            board[row][col] = player_symbol
            display_cell = '0' if player_symbol == 'O' else player_symbol
            print(f"Вы поставили {display_cell} в клетку ({row+1}, {col+1})")
        else:
            # Ход компьютера
            display_symbol = '0' if computer_symbol == 'O' else computer_symbol
            print(f"\n--- Ход компьютера ({display_symbol}) ---")
            row, col = computer_move(board, computer_symbol)
            if row is not None and col is not None:
                board[row][col] = computer_symbol
                display_cell = '0' if computer_symbol == 'O' else computer_symbol
                print(f"Компьютер поставил {display_cell} в клетку ({row+1}, {col+1})")
            else:
                print("Нет свободных клеток!")
        
        # Проверка результата игры
        result = check_winner(board)
        
        if result == player_symbol:
            print_board(board)
            print("\n" + "=" * 30)
            print("ПОЗДРАВЛЯЮ! ВЫ ВЫИГРАЛИ!")
            print("=" * 30)
            break
        elif result == computer_symbol:
            print_board(board)
            print("\n" + "=" * 30)
            print("КОМПЬЮТЕР ВЫИГРАЛ!")
            print("=" * 30)
            break
        elif result == 'draw':
            print_board(board)
            print("\n" + "=" * 30)
            print("НИЧЬЯ!")
            print("=" * 30)
            break
        
        # Смена хода
        player_turn = not player_turn

def main():
    print("=" * 50)
    print("ДОБРО ПОЖАЛОВАТЬ В ИГРУ 'КРЕСТИКИ-НОЛИКИ'!")
    print("=" * 50)
    
    while True:
        play_game()
        
        # Спросить, хочет ли пользователь сыграть снова
        print("\n" + "-" * 40)
        play_again = input("Сыграть снова? (да/нет): ").lower()
        
        if play_again not in ['да', 'д', 'yes', 'y']:
            print("\nСпасибо за игру")
            break
        print("\n" + "=" * 50)
        print("НАЧИНАЕМ НОВУЮ ИГРУ!")
        print("=" * 50)

if __name__ == "__main__":
    main()
