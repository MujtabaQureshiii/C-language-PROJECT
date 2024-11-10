#include <stdio.h>
#include <stdlib.h>
#include <time.h>

#define SIZE 3

void printBoard(char board[SIZE][SIZE]) {
    printf("\n");
    int i, j;
    for (i = 0; i < SIZE; i++) {
        for (j = 0; j < SIZE; j++) {
            printf(" %c ", board[i][j]);
            if (j < SIZE - 1) printf("|");
        }
        printf("\n");
        if (i < SIZE - 1) printf("--- --- ---\n");
    }
    printf("\n");
}

int checkWin(char board[SIZE][SIZE], char player) {
    int i;
    for (i = 0; i < SIZE; i++) {
        if ((board[i][0] == player && board[i][1] == player && board[i][2] == player) || 
            (board[0][i] == player && board[1][i] == player && board[2][i] == player)) {
            return 1;
        }
    }
    if ((board[0][0] == player && board[1][1] == player && board[2][2] == player) || 
        (board[0][2] == player && board[1][1] == player && board[2][0] == player)) {
        return 1;
    }
    return 0;
}

int isBoardFull(char board[SIZE][SIZE]) {
    int i, j;
    for (i = 0; i < SIZE; i++) {
        for (j = 0; j < SIZE; j++) {
            if (board[i][j] == ' ') {
                return 0;
            }
        }
    }
    return 1;
}

void computerMove(char board[SIZE][SIZE]) {
    int row, col;
    srand(time(0));
    do {
        row = rand() % SIZE;
        col = rand() % SIZE;
    } while (board[row][col] != ' ');
    board[row][col] = 'O';
}

int main() {
    char board[SIZE][SIZE] = { {' ', ' ', ' '}, {' ', ' ', ' '}, {' ', ' ', ' '} };
    char currentPlayer = 'X';
    int row, col, choice;
    char player1[50], player2[50];

    printf("Welcome to Tic-Tac-Toe!\n");
    printf("Enter 1 to play against Computer\n");
    printf("Enter 2 for Two-Player Game\n");
    printf("Enter 3 to Exit\n");
    printf("Choice: ");
    scanf("%d", &choice);
    
    if (choice == 3) {
        printf("Exiting the game. Goodbye!\n");
        return 0;
    }

    if (choice == 2) {
        printf("Enter name for Player 1 (X): ");
        scanf("%s", player1);
        printf("Enter name for Player 2 (O): ");
        scanf("%s", player2);
    } else if (choice == 1) {
        printf("Enter your name (X): ");
        scanf("%s", player1);
        snprintf(player2, sizeof(player2), "Computer");
    } else {
        printf("Invalid choice! Exiting.\n");
        return 0;
    }


    while (1) {
        printBoard(board);

        if (choice == 1 && currentPlayer == 'O') {
            printf("%s's (Computer's) turn.\n", player2);
            computerMove(board);
        } else {
            printf("%s, enter row (1-3) and column (1-3): ", currentPlayer == 'X' ? player1 : player2);
            scanf("%d %d", &row, &col);
            row--; col--;

            if (row < 0 || row >= SIZE || col < 0 || col >= SIZE || board[row][col] != ' ') {
                printf("Invalid move! Enter move again.\n");
                continue;
            }
            board[row][col] = currentPlayer;
        }

        if (checkWin(board, currentPlayer)) {
            printBoard(board);
            printf("%s wins!\n", currentPlayer == 'X' ? player1 : player2);
            break;
        } else if (isBoardFull(board)) {
            printBoard(board);
            printf("It's a draw!\n");
            break;
        }

        currentPlayer = (currentPlayer == 'X') ? 'O' : 'X';
    }

    return 0;
}
