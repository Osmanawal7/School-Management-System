#include <iostream>
#include <string>
#include <vector>
#include <sstream>
#include <fstream>
using namespace std;

// Define a Student structure to hold student information
struct Student {
    string indexNumber;
    string name;
    string program;
};

// Define a LectureSession structure to hold session information
struct LectureSession {
    string courseCode;
    string date;
    string startTime;
    int duration;
    vector<string> attendance; // Store attendance status for each student
};

// Create vectors to store registered students and lecture sessions
vector<Student> students;
vector<LectureSession> sessions;

// Function to register a new student
void registerStudent() {
    Student newStudent;
    cout << "\n--- Register New Student ---\n";
    cout << "Enter student index number: ";
    cin >> newStudent.indexNumber;
    cout << "Enter student name: ";
    cin.ignore();
    getline(cin, newStudent.name);
    cout << "Enter student program: ";
    getline(cin, newStudent.program);

    students.push_back(newStudent);
    cout << "Student registered successfully!\n";
}

// Function to view all registered students
void viewAllStudents() {
    if (students.empty()) {
        cout << "\nNo students registered yet!\n";
        return;
    }

    cout << "\n--- Registered Students ---\n";
    cout << "Index Number\tName\t\tProgram\n";
    for (const auto& student : students) {
        cout << student.indexNumber << "\t\t" << student.name << "\t" << student.program << endl;
    }
}

// Function to search for a student by index number
void searchStudentByIndex() {
    string searchIndex;
    cout << "\nEnter student index number to search: ";
    cin >> searchIndex;

    for (const auto& student : students) {
        if (student.indexNumber == searchIndex) {
            cout << "\n--- Student Found ---\n";
            cout << "Index Number: " << student.indexNumber << endl;
            cout << "Name: " << student.name << endl;
            cout << "Program: " << student.program << endl;
            return;
        }
    }
    cout << "Student with index number '" << searchIndex << "' not found!\n";
}

// Function to create a new lecture session
void createLectureSession() {
    LectureSession newSession;
    cout << "\n--- Create Lecture Session ---\n";
    cout << "Enter course code: ";
    cin >> newSession.courseCode;
    cout << "Enter date (DD/MM/YYYY): ";
    cin >> newSession.date;
    
    cout << "Enter start time (minutes): ";
    cin >> newSession.startTime;
    
    cout << "Enter duration (minutes): ";
    cin >> newSession.duration;

    // Initialize attendance for each student
    for (size_t i = 0; i < students.size(); ++i) {
        newSession.attendance.push_back("Absent");
    }

    sessions.push_back(newSession);
    cout << "Lecture session created successfully!\n";
}

// Function to mark attendance for a session
void markAttendance() {
    if (sessions.empty()) {
        cout << "\nNo lecture sessions created yet!\n";
        return;
    }

    cout << "\n--- Mark Attendance ---\n";
    cout << "Select session:\n";
    for (size_t i = 0; i < sessions.size(); ++i) {
        cout << i + 1 << ". " << sessions[i].courseCode << " - " << sessions[i].date << endl;
    }

    int sessionChoice;
    cin >> sessionChoice;
    sessionChoice--;

    if (sessionChoice < 0 || sessionChoice >= static_cast<int>(sessions.size())) {
        cout << "Invalid session choice!\n";
        return;
    }

    cout << "\n--- Students in Session ---\n";
    for (size_t i = 0; i < students.size(); ++i) {
        cout << i + 1 << ". " << students[i].name << " (" << students[i].indexNumber << ")\n";
    }

    int studentChoice;
    string status;
    cout << "Enter student number to mark attendance: ";
    cin >> studentChoice;
    studentChoice--;

    cout << "Enter attendance status (Present/Absent/Late): ";
    cin >> status;

    if (studentChoice >= 0 && studentChoice < static_cast<int>(students.size())) {
        sessions[sessionChoice].attendance[studentChoice] = status;
        cout << "Attendance marked successfully!\n";
    } else {
        cout << "Invalid student choice!\n";
    }
}

// Function to display attendance list for a session
void displayAttendanceList() {
    if (sessions.empty()) {
        cout << "\nNo lecture sessions created yet!\n";
        return;
    }

    cout << "\n--- Display Attendance List ---\n";
    cout << "Select session:\n";
    for (size_t i = 0; i < sessions.size(); ++i) {
        cout << i + 1 << ". " << sessions[i].courseCode << " - " << sessions[i].date << endl;
    }

    int sessionChoice;
    cin >> sessionChoice;
    sessionChoice--;

    if (sessionChoice < 0 || sessionChoice >= static_cast<int>(sessions.size())) {
        cout << "Invalid session choice!\n";
        return;
    }

    cout << "\n--- Attendance List ---\n";
    cout << "Student\t\tStatus\n";
    for (size_t i = 0; i < students.size(); ++i) {
        cout << students[i].name << "\t\t" << sessions[sessionChoice].attendance[i] << endl;
    }
}

// Function to save student records to file
void saveStudentsToFile() {
    ofstream outFile("students.txt");
    if (!outFile) {
        cout << "Error opening file for writing!\n";
        return;
    }

    for (const auto& student : students) {
        outFile << student.indexNumber << "," << student.name << "," << student.program << endl;
    }
    outFile.close();
    cout << "Student records saved to file successfully!\n";
}

// Function to load student records from file
void loadStudentsFromFile() {
    ifstream inFile("students.txt");
    if (!inFile) {
        cout << "Error opening file for reading!\n";
        return;
    }

    students.clear();
    string line;
    while (getline(inFile, line)) {
        stringstream ss(line);
        string indexNumber, name, program;
        getline(ss, indexNumber, ',');
        getline(ss, name, ',');
        getline(ss, program, ',');
        students.push_back({indexNumber, name, program});
    }
    inFile.close();
    cout << "Student records loaded from file successfully!\n";
}

int main() {
    loadStudentsFromFile();

    int choice;
    do {
        cout << "\n--- Student & Attendance Management System ---\n";
        cout << "1. Register students\n";
        cout << "2. View all registered students\n";
        cout << "3. Search students by index number\n";
        cout << "4. Create lecture session\n";
        cout << "5. Mark attendance\n";
        cout << "6. Display attendance list\n";
        cout << "7. Save student records to file\n";
        cout << "0. Exit\n";
        cout << "Enter your choice: ";
        cin >> choice;

        switch (choice) {
            case 1:
                registerStudent();
                break;
            case 2:
                viewAllStudents();
                break;
            case 3:
                searchStudentByIndex();
                break;
            case 4:
                createLectureSession();
                break;
            case 5:
                markAttendance();
                break;
            case 6:
                displayAttendanceList();
                break;
            case 7:
                saveStudentsToFile();
                break;
            case 0:
                cout << "Exiting system. Goodbye!\n";
                break;
            default:
                cout << "Invalid choice. Try again.\n";
        }
    } while (choice != 0);

    return 0;
}
