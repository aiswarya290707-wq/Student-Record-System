# Student-Record-System
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define FILE_NAME "students.dat"

struct Student {
    int roll;
    char name[50];
    float marks[3];
    float total;
};

// Function to add a student record
void addStudent() {
    FILE *fp = fopen(FILE_NAME, "ab");
    struct Student s;

    if (fp == NULL) {
        printf("Error opening file!\n");
        return;
    }

    printf("\nEnter Roll Number: ");
    scanf("%d", &s.roll);

    printf("Enter Name: ");
    scanf(" %[^\n]", s.name);

    printf("Enter marks of 3 subjects:\n");
    s.total = 0;
    for (int i = 0; i < 3; i++) {
        printf("Subject %d: ", i + 1);
        scanf("%f", &s.marks[i]);
        s.total += s.marks[i];
    }

    fwrite(&s, sizeof(s), 1, fp);
    fclose(fp);

    printf("Student record added successfully!\n");
}

// Function to display all records
void displayStudents() {
    FILE *fp = fopen(FILE_NAME, "rb");
    struct Student s;

    if (fp == NULL) {
        printf("No records found!\n");
        return;
    }

    printf("\n--- Student Records ---\n");
    while (fread(&s, sizeof(s), 1, fp)) {
        printf("\nRoll No: %d", s.roll);
        printf("\nName: %s", s.name);
        printf("\nMarks: %.2f, %.2f, %.2f",
               s.marks[0], s.marks[1], s.marks[2]);
        printf("\nTotal: %.2f\n", s.total);
    }

    fclose(fp);
}

// Function to generate rank list
void generateRankList() {
    FILE *fp = fopen(FILE_NAME, "rb");
    struct Student s[100];
    int count = 0;

    if (fp == NULL) {
        printf("No records found!\n");
        return;
    }

    // Read all records into array
    while (fread(&s[count], sizeof(struct Student), 1, fp)) {
        count++;
    }
    fclose(fp);

    // Sort students by total marks (descending)
    for (int i = 0; i < count - 1; i++) {
        for (int j = i + 1; j < count; j++) {
            if (s[i].total < s[j].total) {
                struct Student temp = s[i];
                s[i] = s[j];
                s[j] = temp;
            }
        }
    }

    // Display rank list
    printf("\n--- Rank List ---\n");
    for (int i = 0; i < count; i++) {
        printf("\nRank %d", i + 1);
        printf("\nRoll No: %d", s[i].roll);
        printf("\nName: %s", s[i].name);
        printf("\nTotal Marks: %.2f\n", s[i].total);
    }
}

// Main menu
int main() {
    int choice;

    while (1) {
        printf("\n===== Student Record Menu =====\n");
        printf("1. Add Student Record\n");
        printf("2. Display All Records\n");
        printf("3. Generate Rank List\n");
        printf("4. Exit\n");
        printf("Enter your choice: ");
        scanf("%d", &choice);

        switch (choice) {
            case 1:
                addStudent();
                break;
            case 2:
                displayStudents();
                break;
            case 3:
                generateRankList();
                break;
            case 4:
                printf("Exiting program...\n");
                exit(0);
            default:
                printf("Invalid choice! Try again.\n");
        }
    }

    return 0;
}