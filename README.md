def initialize_game():
    """
    Inisialisasi papan permainan dengan 7 lubang kecil untuk setiap pemain dan 2 lumbung.
    """
    return {
        "p1_side": [7] * 7,  # 7 lubang kecil pemain 1
        "p2_side": [7] * 7,  # 7 lubang kecil pemain 2
        "p1_house": 0,       # Lumbung pemain 1
        "p2_house": 0        # Lumbung pemain 2
    }


def display_board(board):
    """
    Menampilkan papan permainan.
    """
    print("\nPapan Permainan:")
    print(f"Pemain 2: {board['p2_side'][::-1]}   Lumbung: {board['p2_house']}")
    print(f"Pemain 1: {board['p1_side']}   Lumbung: {board['p1_house']}\n")


def distribute_seeds(board, side, index):
    """
    Logika distribusi biji dari lubang yang dipilih.
    """
    seeds = board[side][index]
    board[side][index] = 0  # Kosongkan lubang
    current_side = side
    current_index = index

    while seeds > 0:
        # Pindah ke lubang berikutnya
        current_index += 1

        # Jika keluar dari sisi, pindah ke sisi lain atau lumbung
        if current_side == "p1_side" and current_index == 7:
            board["p1_house"] += 1
            current_side = "p2_side"
            current_index = -1
            seeds -= 1
            continue
        elif current_side == "p2_side" and current_index == 7:
            board["p2_house"] += 1
            current_side = "p1_side"
            current_index = -1
            seeds -= 1
            continue

        # Sebar biji ke lubang berikutnya
        if current_index != -1:
            board[current_side][current_index] += 1
        seeds -= 1

    return current_side, current_index  # Kembalikan lokasi biji terakhir


def play_turn(board, player):
    """
    Melakukan satu giliran untuk pemain.
    """
    side = "p1_side" if player == 1 else "p2_side"

    while True:
        try:
            display_board(board)
            print(f"Giliran Pemain {player}")
            choice = int(input(f"Pilih lubang (1-7): ")) - 1
            if 0 <= choice < 7 and board[side][choice] > 0:
                break
            print("Lubang tidak valid atau kosong, pilih lagi.")
        except ValueError:
            print("Input tidak valid, pilih angka 1-7.")

    # Distribusi biji
    last_side, last_index = distribute_seeds(board, side, choice)

    # Jika biji terakhir jatuh di lumbung sendiri, dapat giliran tambahan
    if (player == 1 and last_side == "p1_house") or (player == 2 and last_side == "p2_house"):
        print(f"Pemain {player} mendapat giliran tambahan!")
        play_turn(board, player)

    # Jika biji terakhir jatuh di lubang kosong milik sendiri, ambil biji lawan
    elif last_side == side and last_index != -1 and board[last_side][last_index] == 1:
        opponent_side = "p2_side" if player == 1 else "p1_side"
        opponent_index = 6 - last_index  # Indeks lawan berseberangan
        captured = board[opponent_side][opponent_index]
        board[opponent_side][opponent_index] = 0
        board[side][last_index] = 0
        if player == 1:
            board["p1_house"] += 1 + captured
        else:
            board["p2_house"] += 1 + captured
        print(f"Pemain {player} menangkap {captured} biji dari lawan!")

    return board


def is_game_over(board):
    """
    Mengecek apakah permainan telah berakhir.
    """
    return all(seeds == 0 for seeds in board["p1_side"]) or all(seeds == 0 for seeds in board["p2_side"])


def determine_winner(board):
    """
    Menentukan pemenang berdasarkan biji di lumbung.
    """
    if board["p1_house"] > board["p2_house"]:
        return 1
    elif board["p2_house"] > board["p1_house"]:
        return 2
    else:
        return 0  # Seri


def main():
    """
    Fungsi utama permainan Congklak.
    """
    print("Selamat datang di permainan Congklak!")
    board = initialize_game()
    current_player = 1

    while not is_game_over(board):
        board = play_turn(board, current_player)
        current_player = 2 if current_player == 1 else 1

    display_board(board)
    print("Permainan selesai!")
    winner = determine_winner(board)
    if winner == 0:
        print("Permainan berakhir seri!")
    else:
        print(f"Pemain {winner} memenangkan permainan!")


if __name__ == "__main__":
    main()r
