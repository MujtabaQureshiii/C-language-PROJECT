```c
#include <stdio.h>
#include <stdlib.h>
#include <time.h>
#include <string.h>

#define SIZE 3


// function to dsiplay grid
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


// function to check the winner
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


// function to check if the board is full or not
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


// function to generate a computer move
void computerMove(char board[SIZE][SIZE]) {
    int row, col;
    srand(time(0));
    do {
        row = rand() % SIZE;
        col = rand() % SIZE;
    } while (board[row][col] != ' ');
    board[row][col] = 'O';
}


// main function
int main() {
    char board[SIZE][SIZE];
    char player1[50], player2[50];
    int totalComputerGames = 0, totalMultiplayerGames = 0;
    int wins = 0, losses = 0, draws = 0;
    int mainChoice, gameModeChoice, row, col;
    char currentPlayer;

    // welcome message
    printf("Welcome to Tic-Tac-Toe!\n\n");
    printf("Here are some instructions you must read before playing the game.\n\n");
    printf("1. Get three of your marks ('X' or 'O') in a row, column, or diagonal.\n");
    printf("2. Two players take turns. Player 1 uses 'X', and Player 2 uses 'O'.\n");
    printf("3. On your turn, choose a row (1-3) and column (1-3) to place your mark.\n");
    printf("4. The board is a 3x3 grid, and you can place your mark in any empty space.\n");
    printf("5. The first player to get three marks in a row, column, or diagonal wins.\n");
    printf("6. If the board is full and no one has won, the game is a draw.\n");

    
    printf("\nEnter your name: ");
    scanf("%s", player1);
    while (1) {
        printf("\nDo you want to Start (1) or Exit (2)? ");
        scanf("%d", &mainChoice);

        if (mainChoice == 2) {
            // displaying final stats
            printf("\nThank you for playing, %s!\n", player1);
            printf("Here are your game statistics:\n");
            printf("Total games played against computer: %d\n", totalComputerGames);
            printf("Total multiplayer games played: %d\n", totalMultiplayerGames);
            printf("Total wins: %d\n", wins);
            printf("Total losses: %d\n", losses);
            printf("Total draws: %d\n", draws);
            printf("Goodbye!\n");
            break;
        } else if (mainChoice == 1) {
        	
            // asking for the game mode
            printf("\nEnter 1 to play against Computer\n");
            printf("Enter 2 for Two-Player Game\n");
            printf("Choice: ");
            scanf("%d", &gameModeChoice);

            if (gameModeChoice == 1) {
                totalComputerGames++;
                snprintf(player2, sizeof(player2), "Computer");
            } else if (gameModeChoice == 2) {
                totalMultiplayerGames++;
                printf("Enter name for Player 2: ");
                scanf("%s", player2);
            } else {
                printf("Invalid choice! Returning to main menu.\n");
                continue;
            }
            
            // reseting the board to empty
            memset(board, ' ', sizeof(board));
            currentPlayer = 'X';

            // main loop of the game
            while (1) {
                printBoard(board);

                if (gameModeChoice == 1 && currentPlayer == 'O') {
                    printf("%s's turn.\n", player2);
                    computerMove(board);
                } else {
                    const char *currentPlayerName = (currentPlayer == 'X') ? player1 : player2;
                    printf("%s, enter row (1-3) and column (1-3): ", currentPlayerName);
                    scanf("%d %d", &row, &col);
                    row=row-1; 
					col=col-1;

                    if (row < 0 || row >= SIZE || col < 0 || col >= SIZE || board[row][col] != ' ') {
                        printf("Invalid move! Enter move again.\n");
                        continue;
                    }
                    board[row][col] = currentPlayer;
                }

                if (checkWin(board, currentPlayer)) {
                    printBoard(board);
                    if (currentPlayer == 'X') {
                        printf("%s wins!\n", player1);
                        if (gameModeChoice == 1) wins++;
                    } else {
                        printf("%s wins!\n", player2);
                        if (gameModeChoice == 1) losses++;
                    }
                    break;
                } else if (isBoardFull(board)) {
                    printBoard(board);
                    printf("It's a draw!\n");
                    draws++;
                    break;
                }

                currentPlayer = (currentPlayer == 'X') ? 'O' : 'X';
            }
        } else {
            printf("Invalid input! Please try again.\n");
        }
    }

    return 0;
}

````
