```c
#include <stdio.h>
#include <stdlib.h>
#include <time.h>
#include <string.h>
#include <windows.h>
#define SIZE 3

// Constants for the game logic
#define MAIN_MENU_START 1
#define MAIN_MENU_EXIT 2
#define GAME_MODE_COMPUTER 1
#define GAME_MODE_MULTIPLAYER 2
#define ROWS 3
#define COLS 3

// Constants for colors
#define RESET_COLOR 7       
#define WELCOME_COLOR 9     
#define INSTRUCTION_COLOR 10
#define GRID_COLOR 14       
#define PLAYER_COLOR 13     
#define COMPUTER_COLOR 11   
#define STAT_COLOR 12        

typedef struct {
    int totalComputerGames;
    int totalMultiplayerGames;
    int wins;
    int losses;
    int draws;
} GameStatistics;

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
    do {
        row = rand() % SIZE;
        col = rand() % SIZE;
    } while (board[row][col] != ' ');
    board[row][col] = 'O';
}

void initializeBoard(char board[SIZE][SIZE]) {
    int i, j;
    for (i = 0; i < SIZE; i++) {
        for (j = 0; j < SIZE; j++) {
            board[i][j] = ' ';
        }
    }
}

void saveStatistics(GameStatistics stats) {
    FILE *file = fopen("game_stats.txt", "wb");
    if (file) {
        fwrite(&stats, sizeof(GameStatistics), 1, file);
        fclose(file);
    } else {
        printf("Error opening file for saving statistics.\n");
    }
}

void loadStatistics(GameStatistics *stats) {
    FILE *file = fopen("game_stats.txt", "rb");
    if (file) {
        if (fread(stats, sizeof(GameStatistics), 1, file) != 1) {
            stats->totalComputerGames = 0;
            stats->totalMultiplayerGames = 0;
            stats->wins = 0;
            stats->losses = 0;
            stats->draws = 0;
        }
        fclose(file);
    } else {
        stats->totalComputerGames = 0;
        stats->totalMultiplayerGames = 0;
        stats->wins = 0;
        stats->losses = 0;
        stats->draws = 0;
    }
}

void displayStatistics(GameStatistics stats) {
    int totalGames = stats.wins + stats.losses + stats.draws;
    setColor(STAT_COLOR);
    printf("  **************************************************  \n");
    printf("  *                                                *  \n");
    printf("  *             THANK YOU FOR PLAYING              *  \n");
    printf("  *                                                *  \n");
    printf("  **************************************************  \n\n\n\n");
    printf("Here are your game statistics:\n");
    printf("Total games played against computer: %d\n", stats.totalComputerGames);
    printf("Total multiplayer games played: %d\n", stats.totalMultiplayerGames);
    printf("Total wins: %d\n", stats.wins);
    printf("Total losses: %d\n", stats.losses);
    printf("Total draws: %d\n", stats.draws);
    if (totalGames > 0) {
        printf("Win rate: %.2f%%\n", (stats.wins / (float)totalGames) * 100);
        printf("Loss rate: %.2f%%\n", (stats.losses / (float)totalGames) * 100);
    }
    printf("\n\n\n");
    setColor(RESET_COLOR);
}


int main() {
    char board[SIZE][SIZE];
    char player1[50], player2[50];
    GameStatistics stats;
    loadStatistics(&stats); // Load last statistics at the start
	

    
    if (stats.totalComputerGames > 0 || stats.totalMultiplayerGames > 0) {
        setColor(STAT_COLOR);
		printf("\n\nLast Game Statistics:\n");
        printf("Total games played against computer: %d\n", stats.totalComputerGames);
        printf("Total multiplayer games played: %d\n", stats.totalMultiplayerGames);
        printf("Total wins: %d\n", stats.wins);
        printf("Total losses: %d\n", stats.losses);
        printf("Total draws: %d\n", stats.draws);
    } else {
        printf("No previous game statistics found.\n");
    }
    
    stats.totalComputerGames=0;
    stats.totalMultiplayerGames=0;
    stats.wins=0;
    stats.losses=0;
    stats.draws=0;
    
    
    
    printf("\nEnter your name: ");
    fgets(player1, sizeof(player1), stdin);
    player1[strcspn(player1, "\n")] = 0;  // Remove newline character

    int mainChoice, gameModeChoice, row, col;
    char currentPlayer;

    srand(time(0));

    setColor(WELCOME_COLOR);
    printf("\n\n\n"); 
    printf("  **************************************************  \n");
    printf("  *                                                *  \n");
    printf("  *             Welcome to Tic Tac Toe             *  \n");
    printf("  *                                                *  \n");
    printf("  **************************************************  \n\n\n\n");

    setColor(INSTRUCTION_COLOR);
    printf("Instructions:\n");
    printf("1. Get three of your marks ('X' or 'O') in a row, column, or diagonal.\n");
    printf("2. Two players take turns. Player 1 uses 'X', and Player 2 uses 'O'.\n");
    printf("3. On your turn, choose a row (1-3) and column (1-3) to place your mark.\n");

    while (1) {
        printf("\nDo you want to Start (%d) or Exit (%d)? ", MAIN_MENU_START, MAIN_MENU_EXIT);
        if (scanf("%d", &mainChoice) != 1) {
            printf("Invalid input! Please enter a number.\n");
            while (getchar() != '\n');
            continue;
        }

        if (mainChoice == MAIN_MENU_EXIT) {
            saveStatistics(stats); // Save statistics before exiting
            displayStatistics(stats);
            break;
        } else if (mainChoice == MAIN_MENU_START) {
            printf("Enter %d to play against Computer\n", GAME_MODE_COMPUTER);
            printf("Enter %d for Two-Player Game\n", GAME_MODE_MULTIPLAYER);
            printf("Choice: ");
            if (scanf("%d", &gameModeChoice) != 1) {
                printf("Invalid input! Returning to main menu.\n");
                while (getchar() != '\n');
                continue;
            }

            if (gameModeChoice == GAME_MODE_COMPUTER) {
                stats.totalComputerGames++;
                snprintf(player2, sizeof(player2), "Computer");
            } else if (gameModeChoice == GAME_MODE_MULTIPLAYER) {
                printf("Enter name for Player 2: ");
                getchar();  // Clear the buffer before using fgets
                fgets(player2, sizeof(player2), stdin);
                player2[strcspn(player2, "\n")] = 0;  // Remove newline character
                stats.totalMultiplayerGames++;
            }

            initializeBoard(board);
            currentPlayer = 'X'; // Player 1 starts

            while (1) {
                printBoard(board);
                printf("%s's turn (Player '%c'):\n", currentPlayer == 'X' ? player1 : player2, currentPlayer);

                if (gameModeChoice == GAME_MODE_COMPUTER && currentPlayer == 'O') {
                    computerMove(board);
                } else {
                    printf("Enter row (1-3) and column (1-3): ");
                    if (scanf("%d%d", &row, &col) != 2 || row < 1 || row > 3 || col < 1 || col > 3 || board[row-1][col-1] != ' ') {
                        printf("Invalid move. Try again.\n");
                        while (getchar() != '\n');
                        continue;
                    }
                    board[row-1][col-1] = currentPlayer;
                }

                if (checkWin(board, currentPlayer)) {
                    printBoard(board);
                    if (currentPlayer == 'X') {
                        printf("%s wins!\n", player1);
                        stats.wins++;
                    } else {
                        printf("%s wins!\n", player2);
                        stats.losses++;
                    }
                    break;
                } else if (isBoardFull(board)) {
                    printBoard(board);
                    printf("It's a draw!\n");
                    stats.draws++;
                    break;
                }

                currentPlayer = (currentPlayer == 'X') ? 'O' : 'X'; // Switch player
            }

            saveStatistics(stats); // Save the statistics after the game ends
        }
    }
    return 0;
}

````
