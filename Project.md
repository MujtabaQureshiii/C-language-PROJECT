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


// function for setting colors
void setColor(int color) {
    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), color);
}


// function to print the grid
void printBoard(char board[SIZE][SIZE]) {
    setColor(GRID_COLOR);  
    printf("\n");

    int i, j;

    printf("   +");
    for (j = 0; j < SIZE; j++) {
        printf("-------+");
    }
    printf("\n");

    
    for (i = 0; i < SIZE; i++) {
        printf("   |");
        for (j = 0; j < SIZE; j++) {
            printf("   %c   |", board[i][j]); 
        }
        printf("\n");

        
        printf("   +");
        for (j = 0; j < SIZE; j++) {
            printf("-------+");
        }
        printf("\n");
    }

    setColor(RESET_COLOR);  
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


// function to check is the game is draw
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

 
// function to generate computer move
void computerMove(char board[SIZE][SIZE]) {
    int row, col;
    do {
        row = rand() % SIZE;
        col = rand() % SIZE;
    } while (board[row][col] != ' ');
    board[row][col] = 'O';
}

// function to clear the grid
void initializeBoard(char board[SIZE][SIZE]) {
	
    int i, j;
    for (i = 0; i < SIZE; i++) {
        for (j = 0; j < SIZE; j++) {
            board[i][j] = ' ';
            
        }
    }
}

// function to save the statistics in file
void saveStatistics(GameStatistics stats) {
	
    FILE *file = fopen("game_stats.txt", "wb");
    if (file) {
        fwrite(&stats, sizeof(GameStatistics), 1, file);
        fclose(file);
    } else {
        printf("Error opening file for saving statistics.\n");
    }
}


//function to read the statistics from file
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

// function to display game statistics
void displayStatistics(GameStatistics stats) {
	
    int totalGames = stats.wins + stats.losses + stats.draws;
    setColor(STAT_COLOR);
    
    printf("  **************************************************  \n");
    printf("  *                                                *  \n");
    printf("  *             THANK YOU FOR PLAYING              *  \n");
    printf("  *                                                *  \n");
    printf("  **************************************************  \n\n\n\n");
    printf("\n\n+-------------------------------------------------+\n");
    printf("|           Here are your game statistics         |\n");
    printf("+-------------------------------------------------+\n");
    printf("| %-38s | %-6d |\n", "Total games played against computer", stats.totalComputerGames);
    printf("| %-38s | %-6d |\n", "Total multiplayer games played", stats.totalMultiplayerGames);
    printf("| %-38s | %-6d |\n", "Total wins", stats.wins);
    printf("| %-38s | %-6d |\n", "Total losses", stats.losses);
    printf("| %-38s | %-6d |\n", "Total draws", stats.draws);
    printf("+-------------------------------------------------+\n");

    
    if (totalGames > 0) {
    	
        printf("| %-38s | %-6.1f%%|\n", "Win rate", (stats.wins / (float)totalGames) * 100);
        printf("| %-38s | %-6.1f%%|\n", "Loss rate", (stats.losses / (float)totalGames) * 100);
        printf("+-------------------------------------------------+\n");
    } else {
    	
        printf("| %-38s | %-6s|\n", "Win rate", "N/A");
        printf("| %-38s | %-6s|\n", "Loss rate", "N/A");
        printf("+-------------------------------------------------+\n");
    }
    printf("\n\n\n");
    setColor(RESET_COLOR);
}


// main function
int main() {
    char board[SIZE][SIZE];
    char player1[50], player2[50];
    GameStatistics stats;
    loadStatistics(&stats); 
	

    
    if (stats.totalComputerGames > 0 || stats.totalMultiplayerGames > 0) {
        setColor(STAT_COLOR);
        
        // loading last game statistics
        
		printf("\n\n+----------------------------------------------+\n");
    printf("|               Last Game Statistics           |\n");
    printf("+----------------------------------------------+\n");
    printf("| %-38s | %-3d |\n", "Total games played against computer", stats.totalComputerGames);
    printf("| %-38s | %-3d |\n", "Total multiplayer games played", stats.totalMultiplayerGames);
    printf("| %-38s | %-3d |\n", "Total wins", stats.wins);
    printf("| %-38s | %-3d |\n", "Total losses", stats.losses);
    printf("| %-38s | %-3d |\n", "Total draws", stats.draws);
    printf("+----------------------------------------------+\n");
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
    player1[strcspn(player1, "\n")] = 0;  

    int mainChoice, gameModeChoice, row, col;
    char currentPlayer;

    srand(time(0));

    setColor(WELCOME_COLOR);
    
    printf("\n\n\n"); 
    
    printf("****************************************************  \n");
    printf("*                                                  *  \n");
    printf("*           Welcome to Tic Tac Toe!                *  \n");
    printf("*                                                  *  \n");
    printf("*       Let's play and have some fun!              *  \n");
    printf("*                                                  *  \n");
    printf("****************************************************  \n\n\n\n");

    setColor(INSTRUCTION_COLOR);
    
    printf("*****************************************************************************  \n");
    printf("*            How to Play Tic Tac Toe:                                       *  \n");
    printf("*                                                                           *  \n");
    printf("* 1. The game is played on a 3x3 grid.                                      *  \n");
    printf("* 2. Two players take turns. Player 1 uses 'X', and Player 2 uses 'O'.      *  \n");
    printf("* 3. On your turn, choose a row (1-3) and column (1-3) to place your mark.  *  \n");
    printf("* 4. The first to get 3 in a row, column, or diagonal wins!                 * \n");
    printf("* 5. Enjoy the game!                                                        *  \n");
	printf("*                                                                           *  \n");
	printf("*****************************************************************************  \n\n");


    while (1) {
        
        printf("\n****************************************************\n");
        printf("*                                                  *\n");
        printf("*             Welcome to Tic Tac Toe!              *\n");
        printf("*          Do you want to Start or Exit?           *\n");
        printf("*                                                  *\n");
        printf("****************************************************\n");

        
        printf("\n");
		printf("\nDo you want to Start (%d) or Exit (%d)? ", MAIN_MENU_START, MAIN_MENU_EXIT);
		printf("\n");
        
        if (scanf("%d", &mainChoice) != 1) {
            printf("Invalid input! Please enter a number.\n");
            while (getchar() != '\n');  // Clear the input buffer
            continue;
        }

        if (mainChoice == MAIN_MENU_EXIT) {
            saveStatistics(stats); // Save statistics before exiting
            displayStatistics(stats);
            break;
        } else if (mainChoice == MAIN_MENU_START) {
            printf("\n*****************************************************\n");
            printf("*               Select the Game Mode                *\n");
            printf("*                                                   *\n");
            printf("*  Enter %d to play against Computer                 *\n", GAME_MODE_COMPUTER);
            printf("*  Enter %d for Two-Player Game                      *\n", GAME_MODE_MULTIPLAYER);
            printf("*                                                   *\n");
			printf("*****************************************************\n");
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
                player2[strcspn(player2, "\n")] = 0;
                stats.totalMultiplayerGames++;
            }

            initializeBoard(board);
            currentPlayer = 'X';

            while (1) {
                printBoard(board);
                printf("\n");
                printf("   %s's turn (Player '%c'):\n", currentPlayer == 'X' ? player1 : player2, currentPlayer);
				printf("\n");
                if (gameModeChoice == GAME_MODE_COMPUTER && currentPlayer == 'O') {
                    computerMove(board);
                } else {
                	
                	printf("\n");
                    printf("|-----------------------------------------------------|\n");
        			printf("|  Enter your move (row and column) between 1 and 3:  |\n");
        			printf("|  Example: Enter 1 2 for row 1 and column 2          |\n");
        			printf("|-----------------------------------------------------|\n");
        			printf("\n");
        			printf("YOUR MOVE: ");
        			
        			
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
                    	printf("\n");
                        printf("          %s wins!\n", player1);
                        printf("\n");
                        stats.wins++;
                    } else {
                    	printf("\n");
                        printf("          %s wins!\n", player2);
                        printf("\n");
                        stats.losses++;
                    }
                    break;
                } else if (isBoardFull(board)) {
                    printBoard(board);
                    printf("\n");
                    printf("It's a draw!\n");
                    printf("\n");
                    stats.draws++;
                    break;
                }

                currentPlayer = (currentPlayer == 'X') ? 'O' : 'X'; 
            }

            saveStatistics(stats); // Save the statistics after the game ends
        }
    }
    return 0;
}
````
