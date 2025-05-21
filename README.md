## EX. NO:2 IMPLEMENTATION OF PLAYFAIR CIPHER

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


## Program:
```
NAME: LOGA MITHRA R
REGISTER NUMBER: 212223100027

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <ctype.h>
#define SIZE 30
// Function to convert the string to lowercase
void toLowerCase(char plain[], int ps) {
    for (int i = 0; i < ps; i++) {
        if (plain[i] >= 'A' && plain[i] <= 'Z') {
            plain[i] += 32; // Convert to lowercase
        }
    }
}
// Function to remove all spaces in a string
int removeSpaces(char *plain, int ps) {
    int count = 0;
    for (int i = 0; i < ps; i++) {
        if (plain[i] != ' ') {
            plain[count++] = plain[i];
        }
    }
    plain[count] = '\0';
    return count;
}
// Function to generate the 5x5 key square
void generateKeyTable(char key[], int ks, char keyT[5][5]) {
    int i, j, k;
    int *dicty = (int *)calloc(26, sizeof(int)); // A 26 character hashmap to store count of the alphabet

    for (i = 0; i < ks; i++) {
        if (key[i] != 'j') {
            dicty[key[i] - 97] = 2;
        }
    }
    dicty['j' - 97] = 1;

    i = 0;
    j = 0;
    for (k = 0; k < ks; k++) {
        if (dicty[key[k] - 97] == 2) {
            dicty[key[k] - 97] -= 1;
            keyT[i][j] = key[k];
            j++;
            if (j == 5) {
                i++;
                j = 0;
            }
        }
    }

    for (k = 0; k < 26; k++) {
        if (dicty[k] == 0) {
            keyT[i][j] = (char)(k + 97);
            j++;
            if (j == 5) {
                i++;
                j = 0;
            }
        }
    }
    free(dicty);
}
// Function to search for the characters of a digraph in the key square and return their position
void search(char keyT[5][5], char a, char b, int arr[]) {
    if (a == 'j') a = 'i';
    if (b == 'j') b = 'i';

    for (int i = 0; i < 5; i++) {
        for (int j = 0; j < 5; j++) {
            if (keyT[i][j] == a) {
                arr[0] = i;
                arr[1] = j;
            } else if (keyT[i][j] == b) {
                arr[2] = i;
                arr[3] = j;
            }
        }
    }
}
// Function to find the modulus with 5
int mod5(int a) {
    return (a % 5);
}
// Function for preparing the plaintext
int prepare(char str[], int *ptrs) {
    // Handle odd length by adding a filler character
    if (*ptrs % 2 != 0) {
        str[(*ptrs)++] = 'x';
    }
    str[*ptrs] = '\0';
    return *ptrs;
}
// Function for performing the encryption
void encrypt(char str[], char keyT[5][5], int ps) {
    int a[4];
    for (int i = 0; i < ps; i += 2) {
        if (i + 1 == ps) { // If odd length and no filler, skip last character
            break;
        }
        search(keyT, str[i], str[i + 1], a);
        if (a[0] == a[2]) {
            str[i] = keyT[a[0]][mod5(a[1] + 1)];
            str[i + 1] = keyT[a[0]][mod5(a[3] + 1)];
        } else if (a[1] == a[3]) {
            str[i] = keyT[mod5(a[0] + 1)][a[1]];
            str[i + 1] = keyT[mod5(a[2] + 1)][a[1]];
        } else {
            str[i] = keyT[a[0]][a[3]];
            str[i + 1] = keyT[a[2]][a[1]];
        }
    }
}
// Function for performing the decryption
void decrypt(char str[], char keyT[5][5], int ps) {
    int a[4];

    for (int i = 0; i < ps; i += 2) {
        if (i + 1 == ps) { // If odd length and no filler, skip last character
            break;
        }
        search(keyT, str[i], str[i + 1], a);
        if (a[0] == a[2]) {
            str[i] = keyT[a[0]][mod5(a[1] + 4)];
            str[i + 1] = keyT[a[0]][mod5(a[3] + 4)];
        } else if (a[1] == a[3]) {
            str[i] = keyT[mod5(a[0] + 4)][a[1]];
            str[i + 1] = keyT[mod5(a[2] + 4)][a[1]];
        } else {
            str[i] = keyT[a[0]][a[3]];
            str[i + 1] = keyT[a[2]][a[1]];
        }
    }
    // Remove trailing filler character 'x' if it was added
    if (ps > 0 && str[ps - 1] == 'x') {
        str[ps - 1] = '\0';
    }
}
// Function to encrypt using Playfair Cipher
void encryptByPlayfairCipher(char str[], char key[]) {
    char keyT[5][5];
    int ks, ps;
    ks = strlen(key);
    ks = removeSpaces(key, ks);
    toLowerCase(key, ks);
    ps = strlen(str);
    toLowerCase(str, ps);
    ps = removeSpaces(str, ps);
    ps = prepare(str, &ps); // Prepare plaintext
    generateKeyTable(key, ks, keyT);
    encrypt(str, keyT, ps);
}
// Function to decrypt using Playfair Cipher
void decryptByPlayfairCipher(char str[], char key[]) {
    char keyT[5][5];
    int ks, ps;
    ks = strlen(key);
    ks = removeSpaces(key, ks);
    toLowerCase(key, ks);
    ps = strlen(str);
    toLowerCase(str, ps);
    ps = removeSpaces(str, ps);
    generateKeyTable(key, ks, keyT);
    decrypt(str, keyT, ps);
}
// Driver code
int main() {
    char str[SIZE], key[SIZE];
    // Get input from user
    printf("Enter the key: ");
    fgets(key, SIZE, stdin);
    key[strcspn(key, "\n")] = '\0'; // Remove trailing newline
    printf("Enter the plaintext: ");
    fgets(str, SIZE, stdin);
    str[strcspn(str, "\n")] = '\0'; // Remove trailing newline
    // Encrypt using Playfair Cipher
    encryptByPlayfairCipher(str, key);
    printf("Cipher text: %s\n", str);
    // Decrypt the ciphertext
    decryptByPlayfairCipher(str, key);
    printf("Decrypted text: %s\n", str);
    return 0;
}
```

Output:

![image](https://github.com/user-attachments/assets/1412ed0e-12fe-420b-a9c0-555c023d466e)
