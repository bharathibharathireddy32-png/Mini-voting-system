#include <stdio.h>
#include <string.h>
#include <ctype.h>

#define MAX_VOTERS 100
#define MAX_CANDIDATES 5

// ---------------- VOTER STRUCTURE ----------------
struct Voter {
    char voterId[11];   // AAA1234567
    char name[20];
};

// ---------------- CANDIDATE STRUCTURE ----------------
struct Candidate {
    int candidateId;
    char name[20];
    char symbol[20];
    char password[20];
    int voteCount;
};

struct Voter voters[MAX_VOTERS];
struct Candidate candidates[MAX_CANDIDATES];

int voterCount = 0;
int candidateCount = 3;

// ---------------- FUNCTION DECLARATIONS ----------------
int isValidVoterId(char id[]);
void registerAndVote();
void candidateLogin();
void viewResults();
void clearBuffer();

// ---------------- MAIN FUNCTION ----------------
int main() {
    int choice;

    // Initialize candidates
    candidates[0] = (struct Candidate){1, "Nitheesh Kumar", "LOTUS", "Ramesh123", 0};
    candidates[1] = (struct Candidate){2, "Bharathi Reddy", "HAND", "Suresh123", 0};
    candidates[2] = (struct Candidate){3, "Sanjay Varma", "FARMER", "Anjali123", 0};

    do {
        printf("\n===== MINI VOTING SYSTEM =====\n");
        printf("1. Voter: Register & Vote\n");
        printf("2. Candidate Login (View Results)\n");
        printf("3. Exit\n");
        printf("Enter your choice: ");

        if (scanf("%d", &choice) != 1) {
            printf("Invalid input!\n");
            clearBuffer();
            continue;
        }

        switch (choice) {
            case 1:
                registerAndVote();
                break;
            case 2:
                candidateLogin();
                break;
            case 3:
                printf("Exiting system...\n");
                break;
            default:
                printf("Invalid choice!\n");
        }

    } while (choice != 3);

    return 0;
}

// ---------------- CLEAR INPUT BUFFER ----------------
void clearBuffer() {
    while (getchar() != '\n');
}

// ---------------- VALIDATE VOTER ID ----------------
int isValidVoterId(char id[]) {
    if (strlen(id) != 10)
        return 0;

    for (int i = 0; i < 3; i++)
        if (!isupper(id[i]))
            return 0;

    for (int i = 3; i < 10; i++)
        if (!isdigit(id[i]))
            return 0;

    return 1;
}

// ---------------- REGISTER AND VOTE ----------------
void registerAndVote() {
    char id[11], name[30];
    int choice, found = 0;

    if (voterCount >= MAX_VOTERS) {
        printf("Maximum voter limit reached!\n");
        return;
    }

    printf("Enter Voter ID (AAA1234567): ");
    scanf("%10s", id);  // Safe input

    if (!isValidVoterId(id)) {
        printf("Invalid Voter ID format!\n");
        return;
    }

    // Prevent duplicate voting
    for (int i = 0; i < voterCount; i++) {
        if (strcmp(voters[i].voterId, id) == 0) {
            printf("Voter already voted!\n");
            return;
        }
    }

    clearBuffer();
    printf("Enter Voter Name: ");
    scanf("%29[^\n]", name);  // Safe input

    printf("\n--- CANDIDATES & SYMBOLS ---\n");
    for (int i = 0; i < candidateCount; i++) {
        printf("%d. %s [%s]\n",
               candidates[i].candidateId,
               candidates[i].name,
               candidates[i].symbol);
    }

    printf("Enter Candidate ID to vote: ");
    scanf("%d", &choice);

    for (int i = 0; i < candidateCount; i++) {
        if (candidates[i].candidateId == choice) {
            candidates[i].voteCount++;
            strcpy(voters[voterCount].voterId, id);
            strcpy(voters[voterCount].name, name);
            voterCount++;
            printf("Vote cast successfully!\n");
            found = 1;
            break;
        }
    }

    if (!found)
        printf("Invalid Candidate ID!\n");
}

// ---------------- CANDIDATE LOGIN ----------------
void candidateLogin() {
    int id;
    char pass[20];

    printf("Enter Candidate ID: ");
    scanf("%d", &id);

    printf("Enter Password: ");
    scanf("%19s", pass);

    for (int i = 0; i < candidateCount; i++) {
        if (candidates[i].candidateId == id &&
            strcmp(candidates[i].password, pass) == 0) {

            printf("\nLogin Successful!\n");
            viewResults();
            return;
        }
    }

    printf("Invalid Candidate ID or Password!\n");
}

// ---------------- VIEW RESULTS ----------------
void viewResults() {
    int winner = 0;

    printf("\n===== FINAL ELECTION RESULTS =====\n");

    for (int i = 0; i < candidateCount; i++) {
        printf("%s [%s] : %d votes\n",
               candidates[i].name,
               candidates[i].symbol,
               candidates[i].voteCount);

        if (candidates[i].voteCount > candidates[winner].voteCount)
            winner = i;
    }

    printf("\nWinner: %s (%s)\n",
           candidates[winner].name,
           candidates[winner].symbol);
}
