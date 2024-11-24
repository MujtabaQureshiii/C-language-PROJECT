```c
#include <stdio.h>
#include <stdlib.h>
#include <time.h>
#include <string.h>
#include <windows.h>  // For Windows console functions

#define SIZE 3

// Color codes for Windows Console
#define RESET_COLOR 7       // Default color
#define WELCOME_COLOR 9     // Blue
#define INSTRUCTION_COLOR 10 // Green
#define GRID_COLOR 14       // Yellow
#define PLAYER_COLOR 13     // Magenta
#define COMPUTER_COLOR 11   // Cyan
#define STAT_COLOR 12       // Red

void setColor(int color) {
    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), color);
}

void printBoard(char board[SIZE][SIZE]) {
    setColor(GRID_COLOR);
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
    setColor(RESET_COLOR);
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
    char board[SIZE][SIZE];
    char player1[50], player2[50];
    int totalComputerGames = 0, totalMultiplayerGames = 0;
    int wins = 0, losses = 0, draws = 0;
    int mainChoice, gameModeChoice, row, col;
    char currentPlayer;

    setColor(WELCOME_COLOR);
    printf("\n\n\n"); 
    printf("  **************************************************  \n");
    printf("  *                                                *  \n");
    printf("  *             Welcome to Tic Tac Toe             *  \n");
    printf("  *                                                *  \n");
    printf("  **************************************************  \n\n\n\n");

    setColor(INSTRUCTION_COLOR);
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
            setColor(STAT_COLOR);
            printf("\nThank you for playing, %s!\n", player1);
            printf("Here are your game statistics:\n");
            printf("Total games played against computer: %d\n", totalComputerGames);
            printf("Total multiplayer games played: %d\n", totalMultiplayerGames);
            printf("Total wins: %d\n", wins);
            printf("Total losses: %d\n", losses);
            printf("Total draws: %d\n", draws);
            printf("\n\n\n"); 
            printf("  **************************************************  \n");
            printf("  *                                                *  \n");
            printf("  *             THANKYOU FOR PLAYING               *  \n");
            printf("  *                                                *  \n");
            printf("  **************************************************  \n\n\n\n");
            break;
        } else if (mainChoice == 1) {
            printf("\n\n\n"); 
            printf("  **************************************************  \n");
            printf("  *                                                *  \n");
            printf("  *                   STARTING                     *  \n");
            printf("  *                                                *  \n");
            printf("  **************************************************  \n\n\n\n");
            printf("Enter 1 to play against Computer\n");
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
            
            memset(board, ' ', sizeof(board));
            currentPlayer = 'X';

            while (1) {
                printBoard(board);

                if (gameModeChoice == 1 && currentPlayer == 'O') {
                    setColor(COMPUTER_COLOR);
                    printf("%s's turn.\n", player2);
                    computerMove(board);
                } else {
                    const char *currentPlayerName = (currentPlayer == 'X') ? player1 : player2;
                    setColor(PLAYER_COLOR);
                    printf("%s, enter row (1-3) and column (1-3): ", currentPlayerName);
                    scanf("%d %d", &row, &col);
                    row = row - 1; 
                    col = col - 1;

                    if (row < 0 || row >= SIZE || col < 0 || col >= SIZE || board[row][col] != ' ') {
                        printf("Invalid move! Enter move again.\n");
                        continue;
                    }
                    board[row][col] = currentPlayer;
                }

                if (checkWin(board, currentPlayer)) {
                    printBoard(board);
                    if (currentPlayer == 'X') {
                        setColor(PLAYER_COLOR);
                        printf("%s wins!\n", player1);
                        if (gameModeChoice == 1) wins++;
                        if (gameModeChoice == 2) wins++; // Player 1 wins in multiplayer
                    } else {
                        setColor(PLAYER_COLOR);
                        printf("%s wins!\n", player2);
                        if (gameModeChoice == 1) losses++;
                        if (gameModeChoice == 2) losses++; // Player 2 wins in multiplayer
                    }
                    break;
                } else if (isBoardFull(board)) {
                    printBoard(board);
                    setColor(STAT_COLOR);
                    printf("It's a draw!\n");
                    draws++;
                    break;
                }

                if (currentPlayer == 'X') {
    				currentPlayer = 'O';
				} else {
    				currentPlayer = 'X';
				}
            }
        } else {
            printf("Invalid input! Please try again.\n");
        }
    }

    return 0;
}

````
