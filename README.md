![image](https://github.com/user-attachments/assets/19928b9e-186e-45c4-9cad-76c63da7d62d)## EX. NO:2 IMPLEMENTATION OF PLAYFAIR CIPHER

 

## AIM:
 
To write a C program to implement the Playfair Substitution technique.

## DESCRIPTION:

The Playfair cipher starts with creating a key table. The key table is a 5×5 grid of letters that will act as the key for encrypting your plaintext. Each of the 25 letters must be unique and one letter of the alphabet is omitted from the table (as there are 25 spots and 26 letters in the alphabet).

To encrypt a message, one would break the message into digrams (groups of 2 letters) such that, for example, "HelloWorld" becomes "HE LL OW OR LD", and map them out on the key table. The two letters of the diagram are considered as the opposite corners of a rectangle in the key table. Note the relative position of the corners of this rectangle. Then apply the following 4 rules, in order, to each pair of letters in the plaintext:
1.	If both letters are the same (or only one letter is left), add an "X" after the first letter
2.	If the letters appear on the same row of your table, replace them with the letters to their immediate right respectively
3.	If the letters appear on the same column of your table, replace them with the letters immediately below respectively
4.	If the letters are not on the same row or column, replace them with the letters on the same row respectively but at the other pair of corners of the rectangle defined by the original pair.
## EXAMPLE:
![image](https://github.com/Hemamanigandan/EX-NO-2-/assets/149653568/e6858d4f-b122-42ba-acdb-db18ec2e9675)

 

## ALGORITHM:

STEP-1: Read the plain text from the user.
STEP-2: Read the keyword from the user.
STEP-3: Arrange the keyword without duplicates in a 5*5 matrix in the row order and fill the remaining cells with missed out letters in alphabetical order. Note that ‘i’ and ‘j’ takes the same cell.
STEP-4: Group the plain text in pairs and match the corresponding corner letters by forming a rectangular grid.
STEP-5: Display the obtained cipher text.




Program:
```
#include <stdio.h>
#include <string.h>
#include <ctype.h>

#define SIZE 5

char keyTable[SIZE][SIZE];  // Playfair matrix

// Function to remove duplicate characters from the key
void prepareKeyTable(char key[], char table[SIZE][SIZE]) {
    int map[26] = {0};
    int i, j, k = 0;
    char keyProcessed[26] = {0};

    // Convert key to uppercase and remove duplicates
    for (i = 0; key[i] != '\0'; i++) {
        char ch = toupper(key[i]);
        if (ch == 'J') ch = 'I'; // Treat 'J' as 'I'
        if (!map[ch - 'A']) {
            keyProcessed[k++] = ch;
            map[ch - 'A'] = 1;
        }
    }

    // Fill remaining letters
    for (i = 0; i < 26; i++) {
        if (i + 'A' == 'J') continue; // Skip 'J'
        if (!map[i]) {
            keyProcessed[k++] = i + 'A';
            map[i] = 1;
        }
    }

    // Fill the 5x5 matrix
    k = 0;
    for (i = 0; i < SIZE; i++) {
        for (j = 0; j < SIZE; j++) {
            table[i][j] = keyProcessed[k++];
        }
    }
}

// Function to find the row and column of a character in the key table
void findPosition(char letter, int *row, int *col) {
    if (letter == 'J') letter = 'I'; // Treat 'J' as 'I'
    for (int i = 0; i < SIZE; i++) {
        for (int j = 0; j < SIZE; j++) {
            if (keyTable[i][j] == letter) {
                *row = i;
                *col = j;
                return;
            }
        }
    }
}

// Function to prepare the plaintext (convert to digraphs)
void formatPlainText(char text[], char formatted[]) {
    int len = strlen(text), i, j = 0;
    for (i = 0; i < len; i++) {
        if (!isalpha(text[i])) continue; // Ignore non-alphabetic characters
        formatted[j++] = toupper(text[i]);
    }
    formatted[j] = '\0';

    // Insert 'X' between duplicate letters in pairs
    char result[100] = {0};
    int newLen = 0;
    for (i = 0; i < j; i++) {
        result[newLen++] = formatted[i];
        if (i + 1 < j && formatted[i] == formatted[i + 1]) {
            result[newLen++] = 'X';
        }
    }

    // Ensure even length by adding 'X' if needed
    if (newLen % 2 != 0) {
        result[newLen++] = 'X';
    }
    result[newLen] = '\0';
    strcpy(formatted, result);
}

// Function to encrypt using Playfair cipher
void encrypt(char text[]) {
    int i, row1, col1, row2, col2;
    printf("\nEncrypted Text: ");
    for (i = 0; i < strlen(text); i += 2) {
        findPosition(text[i], &row1, &col1);
        findPosition(text[i + 1], &row2, &col2);

        if (row1 == row2) {  // Same row → Shift right
            printf("%c%c", keyTable[row1][(col1 + 1) % SIZE], keyTable[row2][(col2 + 1) % SIZE]);
        } else if (col1 == col2) {  // Same column → Shift down
            printf("%c%c", keyTable[(row1 + 1) % SIZE][col1], keyTable[(row2 + 1) % SIZE][col2]);
        } else {  // Rectangle swap
            printf("%c%c", keyTable[row1][col2], keyTable[row2][col1]);
        }
    }
}

// Function to decrypt using Playfair cipher
void decrypt(char text[]) {
    int i, row1, col1, row2, col2;
    printf("\nDecrypted Text: ");
    for (i = 0; i < strlen(text); i += 2) {
        findPosition(text[i], &row1, &col1);
        findPosition(text[i + 1], &row2, &col2);

        if (row1 == row2) {  // Same row → Shift left
            printf("%c%c", keyTable[row1][(col1 - 1 + SIZE) % SIZE], keyTable[row2][(col2 - 1 + SIZE) % SIZE]);
        } else if (col1 == col2) {  // Same column → Shift up
            printf("%c%c", keyTable[(row1 - 1 + SIZE) % SIZE][col1], keyTable[(row2 - 1 + SIZE) % SIZE][col2]);
        } else {  // Rectangle swap
            printf("%c%c", keyTable[row1][col2], keyTable[row2][col1]);
        }
    }
}

int main() {
    char key[100], plaintext[100], formattedText[100];

    // Input keyword and plaintext
    printf("Enter the keyword: ");
    scanf("%s", key);
    printf("Enter the plaintext: ");
    scanf("%s", plaintext);

    // Prepare the key table
    prepareKeyTable(key, keyTable);

    // Format the plaintext
    formatPlainText(plaintext, formattedText);

    // Display key matrix
    printf("\nPlayfair Key Matrix:\n");
    for (int i = 0; i < SIZE; i++) {
        for (int j = 0; j < SIZE; j++) {
            printf("%c ", keyTable[i][j]);
        }
        printf("\n");
    }

    // Encrypt and Decrypt
    encrypt(formattedText);
    decrypt(formattedText);

    printf("\n");
    return 0;
}
```

Output:

![image](https://github.com/user-attachments/assets/fa4bf0db-5a9e-4239-b1c6-0054c1f8d268)

RESULT:

The implementation of Playfair Substitution technique using C program is successfully executed.

