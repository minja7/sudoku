#include <iostream>
using namespace std;

const int SIZE = 9;  // Veličina Sudoku table

// Funkcija za ispisivanje Sudoku table
void printSudoku(int grid[SIZE][SIZE]) {
    for (int row = 0; row < SIZE; row++) {
        for (int col = 0; col < SIZE; col++) {
            cout << grid[row][col] << " ";
        }
        cout << endl;
    }
}

// Proverava da li je bezbedno postaviti broj u datu ćeliju
bool isSafe(int grid[SIZE][SIZE], int row, int col, int num) {
    // Provera reda
    for (int x = 0; x < SIZE; x++) {
        if (grid[row][x] == num) {
            return false;
        }
    }

    // Provera kolone
    for (int x = 0; x < SIZE; x++) {
        if (grid[x][col] == num) {
            return false;
        }
    }

    // Provera 3x3 podmreže
    int startRow = row - row % 3, startCol = col - col % 3;
    for (int i = 0; i < 3; i++) {
        for (int j = 0; j < 3; j++) {
            if (grid[i + startRow][j + startCol] == num) {
                return false;
            }
        }
    }

    return true;
}

// Proverava da li suma brojeva u svakom redu, koloni i podmreži iznosi 45
bool isSumValid(int grid[SIZE][SIZE]) {
    for (int i = 0; i < SIZE; i++) {
        int rowSum = 0, colSum = 0;
        for (int j = 0; j < SIZE; j++) {
            rowSum += grid[i][j];
            colSum += grid[j][i];
        }
        if (rowSum != 45 || colSum != 45) {
            return false;
        }
    }

    // Provera sume u svakoj 3x3 podmreži
    for (int row = 0; row < SIZE; row += 3) {
        for (int col = 0; col < SIZE; col += 3) {
            int boxSum = 0;
            for (int i = 0; i < 3; i++) {
                for (int j = 0; j < 3; j++) {
                    boxSum += grid[row + i][col + j];
                }
            }
            if (boxSum != 45) {
                return false;
            }
        }
    }

    return true;
}

// Glavna funkcija za rešavanje Sudoku problema koristeći povratno pretraživanje
bool solveSudoku(int grid[SIZE][SIZE]) {
    int row, col;
    bool isEmpty = false;

    // Pronalaženje prazne ćelije
    for (row = 0; row < SIZE; row++) {
        for (col = 0; col < SIZE; col++) {
            if (grid[row][col] == 0) {
                isEmpty = true;
                break;
            }
        }
        if (isEmpty) {
            break;
        }
    }

    // Ako nema praznih ćelija, Sudoku je rešen
    if (!isEmpty) {
        return isSumValid(grid);  // Provera da li su sve sume 45
    }

    // Pokušaj sa brojevima 1 do 9
    for (int num = 1; num <= 9; num++) {
        if (isSafe(grid, row, col, num)) {
            grid[row][col] = num;

            // Rekurzivno pokušava rešenje
            if (solveSudoku(grid)) {
                return true;
            }

            // Ako ne uspe, poništava broj (backtracking)
            grid[row][col] = 0;
        }
    }

    return false;  // Povratak na prethodni korak
}

int main() {
    int grid[SIZE][SIZE] = {{0}};  // Inicijalno prazna Sudoku tabla

    int n;
    cout << "Unesite broj popunjenih pozicija: ";
    cin >> n;

    cout << "Unesite pozicije i vrednosti u formatu (red kolona vrednost):" << endl;
    for (int i = 0; i < n; i++) {
        int row, col, val;
        cin >> row >> col >> val;
        grid[row][col] = val;
    }

    cout << "Unesena Sudoku tabla:" << endl;
    printSudoku(grid);

    if (solveSudoku(grid)) {
        cout << "Resena Sudoku tabla:" << endl;
        printSudoku(grid);
    } else {
        cout << "Ne postoji resenje za unetu Sudoku tablu." << endl;
    }

    return 0;
}
